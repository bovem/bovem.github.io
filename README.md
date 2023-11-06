[![Blog Cover Image](./static/bovem-cover.png)](https://www.avni.sh/)

Blogs on Self-Hosting, Homelab and DevOps Technologies.

## Index
-  <a target=_blank href="https://www.avni.sh/posts/kubernetes/">Kubernetes</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/containers/">Containers</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/container-architecture/">Container Architecture</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/container-lifecycle/">Container Lifecycle</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/container-images/">Container Images</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/building-container-images/">Building Container Images</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/kubernetes-operators/">Kubernetes Operators</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/operators-on-openshift/">Operators on OpenShift</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/operator-sdk/">Operator SDK and Bundle Images</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/helm-charts/">Helm Charts</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/container-network-interfaces/">Container Network Interfaces (CNI)</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/container-storage-interfaces/">Container Storage Interfaces (CSI)</a>
    -  <a target=_blank href="https://www.avni.sh/posts/kubernetes/network-functions/">Network Functions</a>
-  <a target=_blank href="https://www.avni.sh/posts/homelab/">Homelab</a>
    -  <a target=_blank href="https://www.avni.sh/posts/homelab/building-your-own-homelab/">Building Your Own Homelab</a>
-  <a target=_blank href="https://www.avni.sh/posts/go/">Go</a>
    -  <a target=_blank href="https://www.avni.sh/posts/go/go-programming-language/">Go Programming Language</a>
    -  <a target=_blank href="https://www.avni.sh/posts/go/file-handling-in-go/">File Handling in Go</a>
    -  <a target=_blank href="https://www.avni.sh/posts/go/concurrency-in-go/">Concurrency in Go</a>
    -  <a target=_blank href="https://www.avni.sh/posts/go/rest-api-requests-in-go/">REST API Requests in Go</a>
-  <a target=_blank href="https://www.avni.sh/posts/dsa/">Data Structures and Algorithms</a>
    -  <a target=_blank href="https://www.avni.sh/posts/dsa/time-complexity/">Time Complexity</a>
    -  <a target=_blank href="https://www.avni.sh/posts/dsa/arrays-strings-hashmaps/">Arrays, Strings, and HashMaps</a>
      -  <a target=_blank href="https://www.avni.sh/posts/dsa/rabin-karp-substring-search/">Rabin-Karp Substring Search</a>
      -  <a target=_blank href="https://www.avni.sh/posts/dsa/contains-duplicate/">Checking an Array for Duplicate Values</a>
      -  <a target=_blank href="https://www.avni.sh/posts/dsa/identifying-anagrams/">Identifying Anagrams</a>
      -  <a target=_blank href="https://www.avni.sh/posts/dsa/finding-elements-that-sum-up-to-target/">Finding Elements in an Array that Sum Up to a Target Value</a>  
      -  <a target=_blank href="https://www.avni.sh/posts/dsa/group-anagrams-in-an-array/">Group Anagrams in an Array</a>
      -  <a target=_blank href="https://www.avni.sh/posts/dsa/finding-most-frequent-elements-in-an-array/">Finding Most Frequent Elements in an Array</a>
      -  <a target=_blank href="https://www.avni.sh/posts/dsa/building-a-product-array-without-the-element-itself/">Building a Product Array without the Element Itself</a>
      -  <a target=_blank href="https://www.avni.sh/posts/dsa/is-valid-sudoku/">Checking Validity of a Sudoku Grid</a>

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
