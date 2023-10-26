### Building for Development:

To build for **development**, use the following command:

```bash
docker-compose -f docker-compose-dev.yaml --env-file .env up
```

### Building for Test:

To build for **test**, use the following command:

```bash
docker-compose -f docker-compose-test.yaml --env-file .env.test up -d
```

### Building for Prod:

To build for **prod**, use the following command:

```bash
docker-compose -f docker-compose-prod.yaml --env-file .env.prod up -d 
```

---

### If you need to rebuild the Dockerfile use:

```bash
docker-compose -f docker-compose-dev.yaml --env-file .env up --build
```
And change the environment accordingly 

### To stop the containers
```bash
docker-compose -f docker-compose-dev.yaml down
```