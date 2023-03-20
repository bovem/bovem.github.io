[![Blog Cover Image](./static/bovem-cover.png)](https://www.bovem.in/)

Blogs on Self-Hosting, Homelab and DevOps Technologies.

## Index
-  <a target=_blank href="https://www.bovem.in/posts/openshift/">OpenShift</a>
    -  <a target=_blank href="https://www.bovem.in/posts/openshift/containers/">Containers</a>
    -  <a target=_blank href="https://www.bovem.in/posts/openshift/container-architecture/">Container Architecture</a>
    -  <a target=_blank href="https://www.bovem.in/posts/openshift/container-lifecycle/">Container Lifecycle</a>
    -  <a target=_blank href="https://www.bovem.in/posts/openshift/container-images/">Container Images</a>
    -  <a target=_blank href="https://www.bovem.in/posts/openshift/building-container-images/">Building Container Images</a>

## Technologies Used
- [Hugo Static Site Generator](https://gohugo.io/)
- [PaperMod Theme](https://github.com/adityatelange/hugo-PaperMod)

## Local Deployment (with Docker)

### Dev Environment (with Drafts and Future Posts)
1. Change directory to `/deploy/dev`
2. Deploy container
```bash
docker compose up -d
```

### Prod Environment
1. Change directory to `/deploy/prod`
2. Deploy container
```bash
docker compose up -d
```