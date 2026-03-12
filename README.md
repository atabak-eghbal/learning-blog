# Atabak's Learning Blog

A personal, structured learning blog built with [Jekyll](https://jekyllrb.com/) and hosted on [GitHub Pages](https://pages.github.com/). It serves as a living knowledge archive where notes, concept breakdowns, and mini-projects are organized by topic and revisited over time using spaced repetition.

🌐 **Live site:** [atabak-eghbal.github.io/learning-blog](https://atabak-eghbal.github.io/learning-blog)

---

## What's in this repo

Each post follows a consistent structure designed to make learning stick:

- **Summary** — a short recap of the concept
- **Core ideas** — what I can explain in my own words
- **Pitfalls** — common mistakes or misconceptions
- **Mini-quiz** — a quick self-test
- **Next review date + next steps** — when and how to revisit

Posts are grouped into the following topic categories:

| Category | What it covers |
|---|---|
| **DSA** | Data structures & algorithms — sliding window, two pointers, binary search, hash maps, trees, graphs, dynamic programming |
| **System Design** | Scalable architecture patterns, distributed systems concepts |
| **Math** | Mathematical foundations relevant to CS and ML |
| **ML Fundamentals** | Core machine learning concepts and models |
| **MLOps** | Productionizing ML — pipelines, monitoring, deployment |
| **AI Engineering** | Building AI-powered applications and APIs |
| **Agentic AI** | LLM agents, tool use, LangChain, LangGraph |
| **Projects** | End-to-end projects tying concepts together |

---

## Tech stack

- **Jekyll** with the [Minima](https://github.com/jekyll/minima) theme
- Hosted on **GitHub Pages** (auto-deployed from the `main` branch)
- Posts written in **Markdown**

## Running locally

```bash
bundle install
bundle exec jekyll serve
```

Then open `http://localhost:4000/learning-blog` in your browser.