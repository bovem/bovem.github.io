FROM lscr.io/linuxserver/code-server:latest
RUN git config --global user.name bovem && \
    git config --global user.email av_nish@outlook.com
RUN /app/code-server/bin/code-server \
    --install-extension equinusocio.vsc-material-theme \
    --install-extension pkief.material-icon-theme \
    --extensions-dir /config/extensions