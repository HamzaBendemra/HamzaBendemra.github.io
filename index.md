---
layout: home
---

<div style="display: flex; align-items: flex-start; gap: 2rem; margin-bottom: 2rem;">
  <div style="flex-shrink: 0;">
    <img src="/assets/images/hamza-profile.jpg" alt="Hamza Bendemra" style="width: 200px; height: 200px; border-radius: 50%; object-fit: cover;">
  </div>
  <div style="flex: 1;">
    <h2>👋 Hello, I'm Hamza Bendemra</h2>
    <p>Welcome to my site. I'm an <strong>AI & ML Lead</strong> with a passion for solving complex business problems through <strong>AI, data, and intelligent systems</strong>.</p>
    <p>Currently based in Abu Dhabi 🇦🇪, I build production-grade tools that combine <strong>Machine Learning</strong>, <strong>Natural Language Processing</strong>, and <strong>web frameworks</strong> to serve applications to my stakeholders.</p>
  </div>
</div>

---

## 🔍 About Me

🎓 **Academic Background**  
PhD in Engineering, with a strong foundation in applied research, data science, and statistical modeling.

🛠️ **Core Competencies**  
- Machine Learning & NLP (scikit-learn, PyTorch, open-source LLMs)  
- Python Web Development (FastAPI, Streamlit, Plotly)  
- Data Engineering & Automation (SQL, Pandas, Synapse Analytics)  
- CI/CD & Cloud Deployment (Docker, Azure, DevOps)

📈 **Strategic Focus Areas**  
- Leadership development through data  
- AI-driven decision support tools  
- Enabling explainable and human-centered ML

---

## 📌 Featured Projects

> 🔐 *Most of my work is internal-facing, but here are a few highlights and personal projects:*

- **Internal LLM Assistant** – Private GPT-style assistant  
- **Business Insights Dashboard** – Interactive NLP-powered tool for analyzing ML-powered business initiatives results  
- **Text Simplifier API** – FastAPI-based app for transforming complex language into digestible and actionable reports for stakeholders

---

## 📝 Recent Posts

{% for post in site.posts limit:3 %}
- [{{ post.title }}]({{ post.url }}) - *{{ post.date | date: "%b %d, %Y" }}*
{% endfor %}

[View all posts →](/blog)

---

## 🤝 Connect with Me

- 💼 [LinkedIn](https://www.linkedin.com/in/hamzabendemra)  
- 📝 [Medium](https://medium.com/@hamzabendemra) *(writing in progress)*  
- 📧 [Email](mailto:hamza@hamzabendemra.com)

---

> *"I choose to share with purpose—whether to drive innovation, develop others, or grow as a leader. My work is a reflection of that principle."*
