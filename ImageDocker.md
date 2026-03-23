## Run on Another Machine Without Rebuilding

After the images are pushed, another machine can pull and run them directly with Docker.

1.  **Create a Docker network:**
    ```bash
    docker network create pcap-net
    ```

2.  **Pull the required images:**
    ```bash
    docker pull redis:7-alpine
    docker pull eric1099t/pcaptain_byeric-backend:latest
    docker pull eric1099t/pcaptain_byeric-frontend:latest
    ```

3.  **Start Redis:**
    ```bash
    docker run -d --name pcap-redis --restart always --network pcap-net -p 6380:6380 redis:7-alpine redis-server --port 6380
    ```

4.  **Start the backend:**
    ```bash
    docker run -d --name pcap-backend --restart always --network pcap-net -p 7000:7000 -v "C:\pcaps:/pcaps:ro" -e PORT=7000 -e PCAP_ROOT_DIRECTORY=/pcaps -e PCAP_PREFIX_STR="C:\pcaps" -e REDIS_HOST=pcap-redis -e REDIS_PORT=6380 -e PUBLIC_URL=http://localhost:7000 eric1099t/pcaptain_byeric-backend:latest
    ```

5.  **Start the frontend:**
    ```bash
    docker run -d --name pcap-frontend --restart always --network pcap-net -p 5000:80 -e BE_PUBLIC_URL=http://localhost:7000 -e NGINX_PORT=80 eric1099t/pcaptain_byeric-frontend:latest
    ```

6.  **Open the UI:**
    `http://localhost:5000`

Notes:
- Replace `C:\pcaps` with the real host path that contains your `.pcap`, `.pcapng`, or `.cap` files.
- If users access the frontend from another machine on the network, set `BE_PUBLIC_URL` to the Docker host IP, for example `http://192.168.1.20:7000`.
- If a container name is already in use on the target machine, either remove the old container or choose a different `--name`.