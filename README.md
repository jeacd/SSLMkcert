# Configurando HTTPS Local com Mkcert + Nginx

Guia de como gerar um certificado SSL local usando **mkcert** e configurar o **Nginx** para servir um domínio HTTPS local.

---

## 1. Instalar Dependências

```bash
sudo apt update
sudo apt install libnss3-tools wget curl -y
```

---

## 2. Instalar o mkcert

```bash
wget https://dl.filippo.io/mkcert/latest?for=linux/amd64 -O mkcert
chmod +x mkcert
sudo mv mkcert /usr/local/bin/
```

---

## 3. Gerar Certificados Locais

```bash
mkcert testhost
```

Arquivos gerados:

- `testhost.pem`
- `testhost-key.pem`

---

## 4. Organizar Certificados

```bash
sudo mkdir -p /etc/nginx/ssl
sudo cp testhost.pem /etc/nginx/ssl/
sudo cp testhost-key.pem /etc/nginx/ssl/
```

---

## 5. Configurar o Nginx

Crie o arquivo:

```bash
sudo nano /etc/nginx/sites-available/testhost
```

Cole dentro:

```bash
server {
    listen 443 ssl;
    server_name testhost;

    ssl_certificate     /etc/nginx/ssl/testhost.pem;
    ssl_certificate_key /etc/nginx/ssl/testhost-key.pem;

    root /var/www/testhost;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}

server {
    listen 80;
    server_name testhost;
    return 301 https://$host$request_uri;
}
```

---

## 6. Criar Conteúdo de Teste(Caso não tenha um projeto para testar)

```bash
sudo mkdir -p /var/www/testhost
echo "<h1>Site HTTPS local funcionando</h1>" | sudo tee /var/www/testhost/index.html
```

---

## 7. Ativar a Configuração e Reiniciar o Nginx

```bash
sudo ln -s /etc/nginx/sites-available/testhost /etc/nginx/sites-enabled/
sudo nginx -t
sudo service nginx restart
```

---

## 8. Registrar o Host Local

```bash
sudo nano /etc/hosts
```

Adicionar a linha:

```
127.0.0.1   testhost
```

---

## 9. Testar

Navegador:

```
https://testhost
```

Terminal:

```bash
curl -vk https://testhost
```

---
