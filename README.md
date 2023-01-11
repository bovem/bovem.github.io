![Blog Cover Image](./static/bovem-cover.png)

Blogs on Mathematics, Computer Science and Technology

## Deployment
Setup a hugo server and a code server for editing

1. Determine machine IP
```bash
export MACHINE_IP=$(ifconfig | grep -Eo 'inet (addr:)?([0-9]*\.){3}[0-9]*' | grep -Eo '([0-9]*\.){3}[0-9]*' | grep -v '127.0.0.1')
```

2. Deploy containers (inside `/deploy` folder)
```bash
docker compose up -d
```

Hugo server should be accessible at http://${MACHINE_IP}:1313
Code server should be accessible at http://${MACHINE_IP}:8443