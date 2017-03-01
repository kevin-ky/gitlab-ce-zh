FROM gitlab/gitlab-ce:8.17.0-ce.0

ENV GITLAB_VERSION=v8.17.0
RUN locale-gen en_US.UTF-8
ENV LANG=en_US.UTF-8 LANGUAGE=en_US:en LC_ALL=en_US.UTF-8

RUN set -xe \
    && echo " # Preparing ..." \
    && curl -sL https://deb.nodesource.com/setup_6.x | bash - \
    && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
    && apt-get update \
    && apt-get install -yqq patch nodejs build-essential yarn \
    && echo " # Generating translation patch ..." \
    && cd / \
    && git clone https://gitlab.com/xhang/gitlab.git \
    && cd gitlab \
    && git diff v8.17.0..v8.17.0-zh > ../zh_CN.diff \
    && echo " # Patching ..." \
    && patch -d /opt/gitlab/embedded/service/gitlab-rails -p1 < ../zh_CN.diff \
    && echo " # Regenerating the assets" \
    && cd /opt/gitlab/embedded/service/gitlab-rails \
    && cp config/gitlab.yml.example config/gitlab.yml \
    && cp config/database.yml.postgresql config/database.yml \
    && cp config/secrets.yml.example config/secrets.yml \
    && rm -rf public/assets \
    && yarn install --pure-lockfile --production \
    && RAILS_ENV=production \
        NO_PRIVILEGE_DROP=true \
        USE_DB=false \
        SKIP_STORAGE_VALIDATION=true \
        bundle exec rake gitlab:assets:clean gitlab:assets:compile \
    && rm -rf log \
        tmp \
        config/gitlab.yml \
        config/database.yml \
        config/secrets.yml \
        .secret \
        .gitlab_shell_secret \
        .gitlab_workhorse_secret \
        node_modules \
    && echo " # Cleaning ..." \
    && apt-get remove -y patch nodejs build-essential \
    && apt-get autoremove -y \
    && rm -rf /gitlab /*.diff /var/lib/apt/lists/*
