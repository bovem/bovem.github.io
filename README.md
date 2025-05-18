[![Blog Cover Image](./static/avnish-cover.png)](https://www.avni.sh/)

Hi I'm Avnish.  
Building software at Autodesk, passionate about simplifying technical concepts.  
This is my blog, where I document my learnings on computer science and mathematics through articles.  
Explore my <a target=_blank href="https://www.avni.sh/posts/projects">personal projects</a> or check my <a target=_blank href="https://www.avni.sh/resume/latest/Resume_Avnish_Pal.pdf">resume</a> for professional experience.  
Connect with me on <a target=_blank href="https://github.com/bovem">GitHub</a> or <a target=_blank href="https://www.linkedin.com/in/avnish-pal/">LinkedIn</a> to discuss ideas or collaborate.  
Subscribe to my <a target=_blank href="https://www.avni.sh/index.xml">RSS feed</a> to be notified when I publish new articles.  

<a target=_blank href="https://www.avni.sh/contents>Index of Blog Contents</a>

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
