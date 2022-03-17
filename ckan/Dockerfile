FROM openknowledge/ckan-base:2.9

MAINTAINER Your Name Here <you@example.com>

COPY nginx.conf /etc/nginx/nginx.conf
COPY ckan.nginx /etc/nginx/sites-available/ckan.conf
COPY entrypoint.sh /entrypoint.sh

RUN apk add --no-cache nginx && \
    rm -rf /etc/nginx/sites-enabled/default && \
    mkdir /etc/nginx/sites-enabled && \
    mkdir -p /run/nginx && \
    mkdir -p /var/log/ckan && \
    touch /run/nginx/nginx.pid && \
    ln -s /etc/nginx/sites-available/ckan.conf /etc/nginx/sites-enabled/ckan && \
    chmod +x /entrypoint.sh

RUN rm -rf /var/log/nginx/ckan && \
    mkdir -p /var/log/nginx/ckan && \
    chown -R nginx:nginx /var/log/nginx && \
    touch /var/log/nginx/error.log && \
    touch /var/log/nginx/access.log

# Set timezone
ARG TZ
RUN echo $TZ > /etc/timezone && \
    cp /usr/share/zoneinfo/$TZ /etc/localtime

# Install any extensions needed by your CKAN instance
# (Make sure to add the plugins to CKAN__PLUGINS in the .env file)
# For instance:
#RUN pip install -e git+https://github.com/ckan/ckanext-pages.git#egg=ckanext-pages && \
#    pip install -e git+https://github.com/ckan/ckanext-dcat.git@v0.0.6#egg=ckanext-dcat && \
#    pip install -r https://raw.githubusercontent.com/ckan/ckanext-dcat/v0.0.6/requirements.txt

# Install the extension(s) you wrote for your own project
# RUN pip install -e git+https://github.com/your-org/ckanext-your-extension.git@v1.0.0#egg=ckanext-your-extension

# Apply any patches needed to CKAN core or any of the built extensions (not the
# runtime mounted ones)
# See https://github.com/okfn/docker-ckan#applying-patchesFF
COPY patches ${APP_DIR}/patches

RUN for d in $APP_DIR/patches/*; do \
        if [ -d $d ]; then \
            for f in `ls $d/*.patch | sort -g`; do \
                cd $SRC_DIR/`basename "$d"` && echo "$0: Applying patch $f to $SRC_DIR/`basename $d`"; patch -p1 < "$f" ; \
            done ; \
        fi ; \
    done

EXPOSE 8080

CMD ["/entrypoint.sh"]