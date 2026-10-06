# Imagem oficial do Nginx com versao fixa
FROM nginx:1.27-alpine

# Autor da imagem
LABEL autor="Mateus de Alcantara Silva" matricula="26127954"

# Copia o site para a pasta que o Nginx publica
COPY html/ /usr/share/nginx/html/

# Documenta a porta do servidor web
EXPOSE 80
