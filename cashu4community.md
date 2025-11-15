1- Instalar Docker

sudo apt install docker.io docker-compose

2- Añadir el usuario actual al grupo docker
Sino conoces el usuario puedes ejecutar el comando 

whoami

sudo usermod -aG docker ubuntu

3- Clonar el repositorio Nutshell 

git clone https://github.com/cashubtc/nutshell.git

4- Crear directorio cashu4community/cashu

mkdir -p cashu4community/cashu

5- Copiar la configuación de ejemplo al archivo .env descargado en el repositorio Nutshell para el directorio cashu4community

cp nutshell/.env.example cashu4community/cashu/.env

6- Cambiar hacia el directorio cashu4community

cd cashu4community

7- Crear archivo docker-compose.yml con lo siguiente

version: '3.8'

services:
 # --- Servicio Nutshell (Cashu mint) ---
  cashu-mint:
    image: cashubtc/nutshell:0.18.1
    container_name: cashu-mint
    restart: unless-stopped
    volumes:
      - ./cashu/.env:/app/.env
      - ./cashu/data:/app/data
    command: ["poetry", "run", "mint"]
    networks:
      - cashu-network

  npm:
    image: jc21/nginx-proxy-manager:latest
    container_name: nginx-proxy-manager
    restart: unless-stopped
    ports:
      # These ports are in format <host-port>:<container-port>
      - '80:80' # Public HTTP Port
      - '443:443' # Public HTTPS Port
      - '81:81' # Admin Web Port
    environment:
      # Uncomment this if you want to change the location of
      # the SQLite DB file within the container
      - DB_SQLITE_FILE:"/data/database.sqlite"
      # Uncomment this if IPv6 is not enabled on your host
      - DISABLE_IPV6:'true'
    volumes:
      - ./npm/data:/data
      - ./npm/letsencrypt:/etc/letsencrypt
    networks:
      - cashu-network
      
networks:
  cashu-network:
    driver: bridge

5- Abrir el archivo .env ubicado en /cashu y cambiar los siguientes valores

nano cashu/.env

6- Abrir el archivo .env o cambiar los siguientes valores. Para generar el MINT_PRIVATE_KEY puedes usar el comando: "openssl rand -hex 32"

nano .env

MINT_LISTEN_HOST=cashu-mint
MINT_LISTEN_PORT=3338
MINT_BACKEND_BOLT11_SAT=LNbitsWallet
MINT_LNBITS_ENDPOINT=https://lachispa.me
MINT_LNBITS_KEY=980ae37241d349b5be29032e14b644f0
MINT_PRIVATE_KEY=5a727e8d72979d2ef35e52a107c02c23fbb121bbcd09d6bfd3618865a6dc2ffe
MINT_DATABASE=data/mint



5- Construimos y levantamos docker

docker-compose up -d



