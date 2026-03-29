I created this script, which bootstraps a new managed node container using the Image created earlier. 
The script:
 - Ensures the image and dedicated network exists, if not it creates it.  
 - Then determines the next container number, port number, and IP address.  
 - Runs the container, places it on the network, and assigns appropriate IP and port numbers.
 - Copies the hosts public key and stes the permissions
 - 
----------------------------------------------------------------------------------------------------------
#!/usr/bin/env bash

set -e

IMAGE_NAME="managed_node"
NETWORK_NAME="ansible_lab_net"
CONTAINER_PREFIX="server"
BASE_PORT=2200
BASE_IP="10.10.10."
START_IP=10                     # First usable IP: 10.10.10.10
SSH_KEY="$HOME/.ssh/id_ed25519.pub"

# --- Ensure image exists ---
if ! podman image exists "$IMAGE_NAME"; then
    echo "Image '$IMAGE_NAME' not found. Build it first:"
    echo "  sudo podman build -t $IMAGE_NAME ."
    exit 1
fi

# --- Ensure network exists ---
if ! podman network exists "$NETWORK_NAME"; then
    echo "Network '$NETWORK_NAME' does not exist. Create it first:"
    echo "  ./create_podman_network.sh"
    exit 1
fi

# --- Determine next container number ---
highest_num=0
for name in $(podman ps -a --format "{{.Names}}"); do
    if [[ $name =~ ${CONTAINER_PREFIX}([0-9]+) ]]; then
        num="${BASH_REMATCH[1]}"
        (( num > highest_num )) && highest_num=$num
    fi
done

next_num=$((highest_num + 1))
container_name="${CONTAINER_PREFIX}${next_num}"

# --- Determine next SSH port ---
highest_port=0
for port in $(podman ps --format "{{.Ports}}" | grep -oE '[0-9]+->22' | cut -d'-' -f1); do
    (( port > highest_port )) && highest_port=$port
done

if (( highest_port < BASE_PORT )); then
    next_port=$BASE_PORT
else
    next_port=$((highest_port + 1))
fi

# --- Determine next IP address ---
highest_ip=0
for ip in $(podman ps --format "{{.Networks}}" | grep -oE "${BASE_IP}[0-9]+"); do
    last_octet="${ip##*.}"
    (( last_octet > highest_ip )) && highest_ip=$last_octet
done

if (( highest_ip < START_IP )); then
    next_ip=$START_IP
else
    next_ip=$((highest_ip + 1))
fi

container_ip="${BASE_IP}${next_ip}"

echo "Creating container: $container_name"
echo "SSH Port: $next_port -> 22"
echo "IP Address: $container_ip"

# --- Run the container ---
podman run -d \
    --name "$container_name" \
    --network "$NETWORK_NAME" \
    --ip "$container_ip" \
    -p ${next_port}:22 \
    "$IMAGE_NAME"

# --- Inject SSH key ---
if [[ -f "$SSH_KEY" ]]; then
    echo "Injecting SSH key..."
    podman exec "$container_name" mkdir -p /home/ansible/.ssh
    podman exec -i "$container_name" bash -c "cat >> /home/ansible/.ssh/authorized_keys" < "$SSH_KEY"
    podman exec "$container_name" chown -R ansible:ansible /home/ansible/.ssh
    podman exec "$container_name" chmod 700 /home/ansible/.ssh
    podman exec "$container_name" chmod 600 /home/ansible/.ssh/authorized_keys
else
    echo "No SSH key found at $SSH_KEY — skipping key injection."
fi

# --- Health check ---
echo "Waiting for SSHD..."
for i in {1..20}; do
    if podman exec "$container_name" pgrep sshd >/dev/null 2>&1; then
        echo "SSHD is running."
        break
    fi
    sleep 0.5
done

# --- Output inventory line ---
echo
echo "Add this to your Ansible inventory:"
echo "${container_name} ansible_host=localhost ansible_port=${next_port} ansible_user=ansible ansible_password=ansible ansible_ssh_common_args='-o StrictHostKeyChecking=no'"
echo
echo "Container '$container_name' is ready."
