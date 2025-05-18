---
author: "Avnish"
title: "brag: A Command Line Tool for Building a Bragging Document"
date: "2024-03-19"
description: "Create and maintain a bragging document from your development environment"
tags: ["projects","go","go-cli","cobra-cli","ollama","AI","LLM","machine-learning","programming"]
categories: ["Projects"]
series: ["Personal Projects"]
aliases: ["brag"]
ShowToc: true
TocOpen: false
comments: false
---

Inspired by Julia Evan's blog <a href="https://jvns.ca/blog/brag-documents/" target="_blank">Get your work recognized: write a brag document</a>
I created this tool to keep a record of my brags in Markdown Format and 
refer them later to create a formatted bragging document.

A bragging or hype document is created to keep a record of achievements over time.
This document/information extracted from this document could be shared with managers 
or used for self-reflection and improvement. I refer to it during my daily standups
and weekly sync-up meetings.

Before this, I was using Google Docs to maintain a record of my accomplishments
but I decided to create a command line tool because:
* I won't have to leave my development environment to add a new accomplishment. 
* I can edit and maintain the document using applications like VSCode, VIM, and Obsidian.
* I can use Git for Version Control. 

**Source Code**: <a href="https://github.com/bovem/brag" target="_blank">github.com/bovem/brag</a>

## Download/Update
```bash
curl -L https://github.com/bovem/brag/releases/download/v0.2/brag-linux-amd64 > /usr/local/bin/brag
```

## Setup and Usage

### Initialize
To initialize a bragging document directory create a Git repository on any provider
like GitHub, GitLab, BitBucket, etc., and clone the repository locally. You can also
create a local Git repository using `git init`.

The location of the bragging document directory is specified using the `BRAG_DOCS_LOC`
environment variable.
```bash
export BRAG_DOCS_LOC=/work-docs
```

Once the directory is available locally you can initialize the document directory
using the `init` subcommand.
```bash
brag init
```

### Adding a new brag
You can add your brags using the `-c`/`--comment` flag.
```bash
brag -c "YOUR TEXT HERE"
```

The brags will be added to the directory specified in the `BRAG_DOCS_LOC` environment
variable.

### Review your brags
To review brags added in a given time period use subcommand `about`.
```bash
brag about last-week
```

If the `BRAG_DOCS_REPO_SYNC` environment variable is set to `true` then new brags
will be committed and pushed to the Git remote.

#### Sample Output
```text
2024-01-02                                                                                                                                        
* 11:12:50      Updated Quarterly Status Report
* 17:17:42      Knowledge transfer presentation with new interns

2024-01-03
* 12:08:36      Filed an issue with UI text rendering on the pre-production environment. ID:12345

2024-01-04
* 17:39:50      Provisioned a new Kubernetes cluster
```

### Summarize your brags using self-hosted LLMs
To summarize the accomplishments and build a draft for a bragging document use the command
`summarize` with a timeframe (similar to the `about` command).
```bash
brag summarize last-month
```
If <a href="/posts/homelab/self-hosting-ollama/" target="_blank">Ollama</a> is deployed and the 
`OLLAMA_URL` environment variable is pointing to your Ollama deployment then
a draft bragging document will be generated on your terminal.

Before generating the document the summarize command will ask for the names of your personal
and work projects, the name of your blog, and other content creation channels to differentiate 
your work and personal updates in the summarized document.

By default, it will use the following prompt for the model but you can
pass your prompt using the `--prompt` flag. 

```text
Draft a bragging document from my perspective
using only the journal data provided below.

The document should be written in a formal tone as it has to be shared
with my manager and stakeholders and it impacts my personal growth.
Omit the fields you think don't have enough information in journals.

The document should be in the following format, the project names and content
mentioned in following format is just an example don't use it in the final output
Use the pointers provided in the format below and omit them in the final output:

# Work Accomplishments
Insert the list of personal work done in great detail, mentioning important
hyperlinks like Jira tickets, pull request links, and other relevant document links
. It should be grouped by projects.

Emphasize the following points
* If I designed or built something from scratch
* Useful insights during the design or coding process
* Impact of individual projects
* Using numbers to show impact like reduced response time by X%, decreased cloud costs by X%.

# Collaboration and Mentorship
Write about collaboration and mentorship work done by me in bullet points.

Emphasize the following points
* Write about each point in great detail supporting it with hyperlinks when necessary
* Write about the conclusion from each point. As in the final impact of helping the intern
* Points about helping others with the skills that I know.
* Improving monitoring and logging
* Code reviews and merge request collaborations
* Important questions and meetings attended
* Giving talks or conducting workshops and hackathons internally

# Design and Documentation
Write about design and documentation efforts.

# Company Building
Write in bullet points about the interviewing and recruiting process contributions from my side.
Also write about contributions to the new hire guide, onboarding documents, and Knowledge Transfer sessions
with the new hires.

# Learning
Write about tools and technologies learned throughout the period and how they could be useful in our
current workflows.

# Outside of Work
Write about blog posts, videos, or any other content creation outside the work that is somehow related to 
the work. Also, write about 
* Talks and event participation
* Contributing to OpenSource projects
* Recognition from any other part of the industry. Like awards.

Additional information about personal projects and blogs will be mentioned after the journal.
Do not add additional information which is not present in the journal.
```

To select a different model than `llama2:latest` use the flag `--model`.

#### Sample Output
```text
Enter the name of your work projects:
Enter the name of your personal projects:
Enter the name of your blog, youtube or any other content creation channels:
Draft Bragging Document

Introduction:

I am excited to share my accomplishments and growth over the past year with you, our esteemed manager and stakeholders. As an integral member of our team, I have been actively contributing to various projects, collaborating with colleagues, and learning new tools and technologies. This document highlights my work achievements, collaboration and mentorship efforts, design and documentation, company building, learning, and outside work contributions.

Work Accomplishments:

1. Project XYZ: As a member of the development team, I designed and built a comprehensive dashboard for monitoring client satisfaction. The dashboard has been instrumental in providing real-time insights into client feedback, enabling us to address their concerns promptly. (hyperlink to Jira ticket)
2. Project ABC: I collaborated with the UX/UI team to redesign a critical feature of the application. My contributions included designing new layouts and providing constructive feedback during the user testing phase, resulting in an improved user experience. (hyperlink to design document)
3. Project DEF: I contributed to the development of a mobile application for a client's business needs. My responsibilities included writing clean, efficient, and well-documented code, ensuring seamless functionality across various platforms. (hyperlink to pull request)

Collaboration and Mentorship:

1. Mentored an intern on the basics of web development, providing guidance on project management, coding best practices, and problem-solving techniques. The intern demonstrated significant improvement throughout the collaboration, showcasing their newfound skills. (hyperlink to mentorship session)
2. Collaborated with cross-functional teams to develop innovative solutions for a client's complex business problem. Through active listening and ideation, we generated several potential ideas and approaches, which were well received by the team and stakeholders. (hyperlink to brainstorming session)
3. Contributed to the creation of an internal knowledge transfer program, providing structured training sessions for new hires. This initiative enhanced the onboarding process, ensuring a smooth transition into our team's workflow. (hyperlink to knowledge transfer session)

Design and Documentation:

1. Created comprehensive design documents for each project, including wireframes, mockups, and style guides. These resources were instrumental in communicating design intent to the development team and stakeholders. (hyperlink to design document)
2. Developed a standard operating procedure (SOP) for our team's documentation process, streamlining the creation and maintenance of technical documents. This initiative has resulted in increased efficiency and consistency across our projects. (hyperlink to SOP document)

Company Building:

1. Participated in the interviewing and recruiting process, contributing to the identification and hiring of top talent for our team. (hyperlink to job postings)
2. Created an updated new hire guide, providing comprehensive information on our team's workflow, tools, and best practices. This document has been instrumental in ensuring a smooth transition for new team members. (hyperlink to new hire guide)
3. Contributed to the development of internal training programs, enhancing the skills of our team members and fostering a collaborative learning environment. (hyperlink to training program)

Learning:

1. Expanded my knowledge of emerging technologies such as React Native, GraphQL, and Machine Learning. Through hands-on projects and collaboration with colleagues, I have gained a deeper understanding of these tools and their potential applications in our workflows. (hyperlink to personal project)
2. Participated in industry events, conferences, and meetups, showcasing our team's work and engaging with peers. These experiences have provided valuable insights into the latest trends and innovations in our field. (hyperlink to event report)

Outside of Work:

1. Published articles on Hacker Noon and other industry platforms, sharing my experiences and insights on web development best practices. The articles have received positive feedback from peers and industry leaders, with several shares and likes on social media platforms. (hyperlink to article)
2. Contributed to OpenSource projects, such as [insert project name], providing new features and enhancements that benefit the broader development community. (hyperlink to OpenSource project)
3. Received recognition from industry leaders and peers for my work in web development, including a mention in [insert publication name] and an award at [insert event name]. (hyperlink to publication or event)

Personal Projects:

1. Developed a personal website showcasing my work and projects, serving as a portfolio and networking tool. (hyperlink to personal website)
2. Created a blog focusing on web development best practices and emerging technologies, providing valuable insights and resources for the industry. (hyperlink to blog)
3. Fostered a community of developers through social media platforms, sharing knowledge and collaborating on projects. (hyperlink to social media channel)

Conclusion:
In conclusion, I have demonstrated significant growth and achievements in my role as a web developer within the past year. Through active collaboration, mentorship, design and documentation, company building, learning, and outside work contributions, I have enhanced our team's workflows, fostered a culture of innovation and excellence, and expanded my skill set. I look forward to continuing to contribute to our team's success in the future.

Additional Information:
Personal Projects:

* Website: [insert website link]
* Blog: [insert blog link]
* Social Media Channel: [insert social media channel link]

Note: The above document is a draft and may require revisions based on your feedback. Please feel free to provide your inputs and suggestions, and I will be more than happy to incorporate them into the final document.
```

<hr>
Thank you for taking the time to read this blog post! If you found this content valuable and would like to stay updated with my latest posts consider subscribing to my <a href="https://www.avni.sh/index.xml" target="_blank">RSS Feed</a>.

# Resources
<a href="https://github.com/bovem/brag" target="_blank">github.com/bovem/brag</a>  
<a href="https://jvns.ca/blog/brag-documents/" target="_blank">Get your work recognized: write a brag document</a>  
<a href="/posts/homelab/self-hosting-ollama/" target="_blank">Ollama</a>  
