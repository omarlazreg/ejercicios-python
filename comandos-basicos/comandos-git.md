```

CONFIGURAR SSH

- git remote set-url origin NUEVA_URL --> Cambia la URL del remoto del repo local para que use SSH (git@github.com:...) en vez de HTTPS, o apunte a otro repo distinto — este paso es independiente de la clave SSH en sí, es solo configuración del repositorio.
- ssh-keygen -t ed25519 -C "tu_email@ejemplo.com" --> Genera el par de claves (privada + pública) en tu equipo. Es el primer paso, sin clave no hay nada que configurar.
- Get-Content ..\.ssh\id_ed25519.pub --> Muestra la clave pública en pantalla para copiarla y pegarla en GitHub (Settings → SSH keys).
- ssh -T git@github.com --> Verifica que GitHub reconoce la clave y la autenticación funciona correctamente.

```