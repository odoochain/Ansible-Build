# Collabora Code role

Deploy Collabora Online Development Edition (CODE) container.

## Usage

Configure the role.

**vars.yml**

```yml
# https://hub.docker.com/r/collabora/code
collabora_code_image: collabora/code:23.05.5.4.1
collabora_code_hostname: doc01
collabora_code_server_name: doc.example.com
collabora_code_aliasgroup1: cloud.example.com
collabora_code_description: Collabora for Nextcloud # default: Collabora Code
collabora_code_port: 9981 # default: 9980
collabora_code_username: code # default: admin
collabora_code_password: # default: "{{ vault_collabora_code_password }}"
```

And include it in your playbook.

```yml
- hosts: nextcloud
  roles:
  - role: collabora_code
```

## Docs

### Nginx config

Setup this Nginx configuration for the `doc01` host: 

```conf
# static files
location ^~ /browser {
  proxy_pass https://doc01:9980;
  proxy_set_header Host $http_host;
}

# WOPI discovery URL
location ^~ /hosting/discovery {
  proxy_pass https://doc01:9980;
  proxy_set_header Host $http_host;
}

# Capabilities
location ^~ /hosting/capabilities {
  proxy_pass https://doc01:9980;
  proxy_set_header Host $http_host;
}

# main websocket
location ~ ^/cool/(.*)/ws$ {
  proxy_pass https://doc01:9980;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "Upgrade";
  proxy_set_header Host $http_host;
  proxy_read_timeout 36000s;
}

# download, presentation and image upload
location ~ ^/(c|l)ool {
  proxy_pass https://doc01:9980;
  proxy_set_header Host $http_host;
}

# Admin Console websocket
location ^~ /cool/adminws {
  proxy_pass https://doc01:9980;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "Upgrade";
  proxy_set_header Host $http_host;
  proxy_read_timeout 36000s;
}
```
