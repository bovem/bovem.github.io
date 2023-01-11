[![Blog Cover Image](./static/bovem-cover.png)](https://www.bovem.in/)

Blogs on Mathematics, Computer Science and Technology

## Deployment
Setup a hugo and code server on local network for editing

1. Determine machine IP. [Source](https://stackoverflow.com/questions/13322485/how-to-get-the-primary-ip-address-of-the-local-machine-on-linux-and-os-x)
```bash
export MACHINE_IP=$(ifconfig | grep -Eo 'inet (addr:)?([0-9]*\.){3}[0-9]*' | grep -Eo '([0-9]*\.){3}[0-9]*' | grep -v '127.0.0.1')
```

2. Deploy containers (inside `/deploy` folder)
```bash
docker compose up -d
```

Hugo server should be accessible at `http://${MACHINE_IP}:1313`  
Code server should be accessible at `http://${MACHINE_IP}:8443`