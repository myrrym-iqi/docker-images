# docker-images

This repository contains files for building [Docker](https://www.docker.com/) images. All images should be available on [Docker Hub](https://hub.docker.com/) under the [juwaicom](https://hub.docker.com/u/juwaicom/) account.

Publishing is manual (no CI builds/pushes these images) - check the tag table for each image below before rebuilding and pushing over an existing tag.

## amazonlinux-laravel-php tags

| Tag | PHP | Built from |
| --- | --- | --- |
| `1.0`, `buildx-latest` | 7.3 | commit `12fbd50` - do **not** rebuild from current master, master is on PHP 7.4 |
| `php74` (pending publish) | 7.4 | commit `882c66a` onward |

`:1.0` is pinned by list.juwai.com, himalayas-cms, website-tiger, redstar, juwai-admin, agent-admin
and juwai-com-v2. Rebuilding and pushing `:1.0` from current master would move all seven from PHP
7.3 to 7.4. Build 7.3 from `git checkout 12fbd50` if you need that image again.
