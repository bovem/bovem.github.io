[![Blog Cover Image](./static/bovem-cover.png)](https://www.bovem.in/)

Blogs on Self-Hosting, Homelab and DevOps Technologies.

## Index
-  <a target=_blank href="https://www.bovem.in/posts/kubernetes/">Kubernetes</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/containers/">Containers</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/container-architecture/">Container Architecture</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/container-lifecycle/">Container Lifecycle</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/container-images/">Container Images</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/building-container-images/">Building Container Images</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/kubernetes-operators/">Kubernetes Operators</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/operators-on-openshift/">Operators on OpenShift</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/operator-sdk/">Operator SDK and Bundle Images</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/helm-charts/">Helm Charts</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/container-network-interfaces/">Container Network Interfaces (CNI)</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/container-storage-interfaces/">Container Storage Interfaces (CSI)</a>
    -  <a target=_blank href="https://www.bovem.in/posts/kubernetes/network-functions/">Network Functions</a>
-  <a target=_blank href="https://www.bovem.in/posts/homelab/">Homelab</a>
    -  <a target=_blank href="https://www.bovem.in/posts/homelab/building-your-own-homelab/">Building Your Own Homelab</a>
-  <a target=_blank href="https://www.bovem.in/posts/go/">Go</a>
    -  <a target=_blank href="https://www.bovem.in/posts/go/go-programming-language/">Go Programming Language</a>
    -  <a target=_blank href="https://www.bovem.in/posts/go/file-handling-in-go/">File Handling in Go</a>
    -  <a target=_blank href="https://www.bovem.in/posts/go/concurrency-in-go/">Concurrency in Go</a>
    -  <a target=_blank href="https://www.bovem.in/posts/go/rest-api-requests-in-go/">REST API Requests in Go</a>
-  <a target=_blank href="https://www.bovem.in/posts/dsa/">Data Structures and Algorithms</a>
    -  <a target=_blank href="https://www.bovem.in/posts/dsa/time-complexity/">Time Complexity</a>
    -  <a target=_blank href="https://www.bovem.in/posts/dsa/arrays-strings-hashmaps/">Arrays, Strings, and HashMaps</a>
    -  <a target=_blank href="https://www.bovem.in/posts/dsa/rabin-karp-substring-search/">Rabin-Karp Substring Search</a>
    -  <a target=_blank href="https://www.bovem.in/posts/dsa/contains-duplicate/">Checking an Array for Duplicate Values</a>
    -  <a target=_blank href="https://www.bovem.in/posts/dsa/identifying-anagrams/">Identifying Anagrams</a>
    -  <a target=_blank href="https://www.bovem.in/posts/dsa/finding-elements-that-sum-up-to-target/">Finding Elements in an Array that Sum Up to a Target Value</a>

## Technologies Used
- [Hugo Static Site Generator](https://gohugo.io/)
- [PaperMod Theme](https://github.com/adityatelange/hugo-PaperMod)

## Local Deployment (with Docker)

### After cloning
```bash
git submodule update --init --recursive
```

### Updating theme
```bash
git submodule update --remote --merge
```

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
