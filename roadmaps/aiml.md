# 8 Weeks to Build Real AI/ML Projects and Know How to Talk About Them

---
## 📌 How to Use This Tracker

This is an 8-week self-paced AI/ML learning system. Each week has:

- A clear focus
- A real-world project
- Core tools you need to learn
- Questions to help you reflect and build real understanding

You can go faster or slower depending on your schedule. You can modify projects to fit your goals. What matters is consistency and depth.

# 📅 WEEK 1: Python, Pandas, and NumPy

This week is about learning how to work with data at the ground level. You’ll be working in Python, building confidence in reading datasets, cleaning them up, transforming columns, and spotting useful patterns.

The focus here is on fluency. You're not aiming to memorize every function. The goal is to get comfortable enough that you can open a new dataset and know what steps to take next. This skill shows up in every part of AI and ML, whether you're prepping features for a model or passing cleaned inputs into a GenAI pipeline.

**Project**: Work with a music dataset and explore trends in songs released over the past few decades. You could look at how genres have changed, which features are common in popular tracks, or how things like energy and danceability vary by year.

**Tools**: Pandas, NumPy, Seaborn or Matplotlib

**🌱 Reflection**:

- What part of your workflow still feels slow
- Which tools or functions became easier after some repetition
- If someone handed you a different dataset, what parts would you feel ready to handle on your own

---

# 📅 WEEK 2: Intro to Machine Learning

This is where you train your first model. You'll go from raw data to a trained classifier and evaluate how well it performs. You're not aiming for perfection. You're learning how models learn, how to measure what they're doing, and how to adjust based on results.

This week is your introduction to the standard supervised learning flow. You’ll understand what it means to split data into training and test sets, how to train multiple models, and how to compare them using real metrics instead of relying on just accuracy.

**Project**: Build a model that predicts whether a loan will default. You'll work with a structured dataset, train a few different models, compare how they perform, and write down what you observe about their behavior and mistakes.

**Tools**: Scikit-learn, Random Forest, Logistic Regression, evaluation metrics like precision, recall, and ROC AUC

**🌱 Reflection**:

- Which evaluation metric did you end up using and why
- What surprised you about how your model performed
- If you had to explain your model to someone non-technical, how would you do it

---

# 📅 WEEK 3: Structured ML Projects and Messy Data

This week is where things start to feel more real. You’ll deal with a structured dataset that has missing values, inconsistent formats, and edge cases that aren't immediately obvious. The goal is to go beyond clean textbook examples and start working like an actual ML engineer.

You’ll spend time engineering features, deciding what to keep or remove, and building models that are good enough to be useful, not perfect. The key here is developing judgment and learning how to work through ambiguity.

**Project**: Use a healthcare dataset to predict hospital readmissions. You’ll need to balance class distribution, decide which patient details matter most, and explain your model’s predictions in a way that makes sense for a high-stakes context.

**Tools**: Scikit-learn, XGBoost, SHAP or feature importance tools

**🌱 Reflection**:

- What tradeoffs did you make in cleaning and selecting features
- How did you interpret your model’s decisions
- What would you do differently if you had to build a version two of this project

---

# 📅 WEEK 4: End-to-End ML with Deployment

This week is about building something you can actually show. You’ll take everything you’ve done so far and wrap it into a simple, usable interface. You’re not building for a Kaggle score. You’re building for someone to use it, test it, and give feedback.

The challenge is to take a model, wrap it in a lightweight app, and make the whole process feel clean. This includes how the data flows in, how results are shown, and how decisions are explained.

**Project**: Build a salary prediction tool based on job listings. Given a title, location, and skill set, the model should estimate a salary range. You’ll also visualize feature contributions so that people understand where the prediction comes from.

**Tools**: Streamlit or Gradio for app building, SHAP for interpretability

**🌱 Reflection**:

- What assumptions did you make in your deployment pipeline
- What would you need to change to make this usable by a wider audience
- Did deploying the model help you understand it better

---

# 📅 WEEK 5: GenAI Foundations

This week marks your shift from classic ML into the world of generative AI. You’ll learn how large language models work, how to use them through APIs, and how to build prompts that return useful, reliable outputs.

You’ll also learn about embeddings and vector search, which are core to building retrieval-augmented tools that feel smarter and more useful. The focus isn’t just on playing with prompts. It’s on learning what makes a prompt consistent, how to handle edge cases, and how to combine LLMs with your own data.

**Project**: Build a research paper assistant that pulls content from an external source, embeds it, and lets users search through it with a smart, LLM-powered interface. You’ll connect documents, prompts, and retrieval into one flow.

**Tools**: OpenAI or Cohere APIs, vector store like FAISS or Chroma, basic Python for orchestration

**🌱 Reflection**:

- What helped you design better prompts
- What failed when you tried to retrieve and summarize content
- How would you improve response relevance or stability

---

# 📅 WEEK 6: Real GenAI Applications

Now that you’ve seen the building blocks, this week is about creating something people would actually want to use. This is where you take an idea and connect LLMs with logic, tools, and interface design.

Your project should have a clear use case, solve a real pain point, and feel like a tool more than a toy. The technical focus is on chaining prompts, using functions or retrieval, and making the interaction smooth from start to finish.

**Project**: Build a resume skill matcher. Someone uploads a resume and selects a target job, and the app returns missing skills, learning resources, and job fit analysis using an LLM behind the scenes.

**Tools**: LangChain or your own prompt chaining logic, LLM APIs, resume parsing libraries, optional UI with Streamlit

**🌱 Reflection**:

- What made this feel useful or not
- How did you design for users who might enter unexpected inputs
- What did you learn about LLM limitations during testing

---

# 📅 WEEK 7: Agent Workflows and Tool Use

This week is where you step into the world of agents. These systems go beyond a single prompt or output. They reason through tasks, use tools when needed, and decide what steps to take based on intermediate results.

You’ll learn how to work with orchestration frameworks, connect tool APIs, and build memory-aware flows. The hard part is knowing when to use an agent and how to keep it from going off track.

**Project**: Build a meeting assistant that reads calendar data and notes, generates action items, follows up on tasks, and helps plan the next meeting. You’ll need to connect APIs, use memory to retain context, and handle failures gracefully.

**Tools**: LangGraph or crewAI, OpenAI Functions or Assistants, any scheduling/calendar API

**🌱 Reflection**:

- How did you balance automation with control
- Where did your agent struggle to complete multi-step flows
- What parts of the workflow felt fragile or unclear

---

# 📅 WEEK 8: Final Capstone: ML, GenAI, and Agents

This is the week where everything comes together. You’re building a real system that combines traditional ML with LLM reasoning and agent orchestration. The goal is to demonstrate that you can plan, build, test, and explain an intelligent workflow that mirrors what teams build in production.

You’ll scope your own project using the pieces you’ve already worked with. You’re not starting from scratch. You’re designing a system that connects everything you’ve learned.

**Project**: Build a smart career advisor. Given a resume and a dream role, it should suggest matching jobs, identify missing skills, estimate salary based on your ML model from earlier, and explain its suggestions clearly using an LLM. You’ll combine structured data, GenAI prompts, and agent logic to produce useful results.

**Tools**: Your choice. Combine what you’ve already used: sklearn models, OpenAI for explanation, a lightweight agent framework, and a clean UI.

**🌱 Reflection**:

- What part of the system made you most proud
- What gaps do you still want to fill
- If someone asked to walk through this project in an interview, what would you emphasize