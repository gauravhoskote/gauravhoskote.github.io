---
title: "Devpost Valorant Champions Tour Hackathon by AWS"
date: 2024-10-26
excerpt: ""
collection: portfolio
---

UPDATE: We have made it to the Top 25 in the Hackathon!

You can take a look at the actual project page here: [link](https://devpost.com/software/valorant-teamforge-ai)

<!-- [![IMAGE ALT TEXT HERE](https://www.youtube.com/watch?v=rhfXfL33KVg)](https://www.youtube.com/watch?v=rhfXfL33KVg) -->

<iframe width="560" height="315" src="https://www.youtube.com/embed/rhfXfL33KVg" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Inspiration
Our inspiration for Valorant TeamForge AI came from the specific objectives set by the VCT Hackathon, where we aimed to create a reliable digital assistant for scouting and recruitment in the competitive VALORANT esports scene. We recognized the limitations of traditional Retrieval-Augmented Generation (RAG) systems, which primarily rely on vector similarity matching. While RAG systems can effectively retrieve information, they often struggle with precision when applying specific filters to structured data, leading to potential inaccuracies.

In contrast, we decided to build our digital assistant using an AI-powered MongoDB query engine integrated with Amazon Bedrock. This approach allows us to ensure high accuracy and reliability in our information retrieval processes. By directly querying structured data based on user prompts, we could deliver precise results tailored to the nuanced requirements of team composition and player performance analysis.

### What it does
Valorant TeamForge AI serves as an AI-powered digital assistant designed to build effective VALORANT teams and answer questions about esports players. By leveraging data sources, the assistant generates team compositions based on various prompts, such as creating professional teams, semi-professional teams, or cross-regional teams. It assigns player roles, explains team effectiveness, and provides insights on strategies, ensuring a comprehensive understanding of the chosen compositions.

### How we built it
We divided our project into two main phases. The first phase involved creating an ETL (Extract, Transform, Load) pipeline to efficiently process and structure data from the VCT S3 bucket. This pipeline generated detailed player profiles, which were then stored in a MongoDB Atlas database linked to our AWS account. We successfully condensed vast amounts of game data, ensuring that the information was both compact and efficient for retrieval by our application.

In the second phase, we developed a web application using Flask, featuring a chat interface powered by AWS Bedrock’s capabilities. By integrating an AI-driven MongoDB query engine, our assistant can interpret natural language prompts and generate precise queries. This allows users to build teams and analyze player performances without needing to understand the underlying database structure.

### Challenges we ran into
Throughout the project, we encountered several technical challenges. One significant hurdle was ensuring the accuracy and relevance of the player profiles generated during the ETL phase. We had to decide on the type of fields we wanted to extract in order to create a player profile. Additionally, we faced difficulties in fine-tuning the AI model to effectively translate user prompts into structured database queries, requiring extensive testing and iteration to optimize performance.

Another challenge was the issue of latency. The model takes quite some time to respond to the queries by calling the relevant data extraction functions. To tackle this issue we decided to incorporate multithreading in the second phase of the project.

### Accomplishments that we're proud of
We take pride in the efficiency of our ETL pipeline, which successfully condensed hundreds of gigabytes of data into just 6MB without losing critical information. Additionally, the seamless integration of our web application allows users to interact with our digital assistant intuitively. The ability to execute follow-up queries while maintaining context adds significant value to user experience, making it a powerful tool for scouting and recruitment.

### What we learned
This project provided us with invaluable insights into data management, AI integration, and user experience design. We learned how to effectively handle data extraction, transformation, and storage, as well as how to optimize MongoDB for structured queries. Most importantly, we discovered the importance of choosing the right technology for the task at hand. Our decision to move away from RAG systems to a more direct querying approach underscored the value of accuracy and reliability in building competitive teams.

### What's next for Valorant TeamForge AI
Looking ahead, we plan to enhance the capabilities of Valorant TeamForge AI by incorporating more advanced analytics, including real-time match data for player performance metrics. We also aim to refine the AI’s natural language processing abilities to improve its understanding of user prompts further. Our ultimate goal is to establish Valorant TeamForge AI as an indispensable resource for both casual gamers and competitive teams, providing precise insights into team building and player performance.

### Second Phase Submission (Finalists):
Although we managed to build an amazing Agent that can create VCT teams according to the given prompts, there was still some room for improvement. The agent could handle almost all the queries mentioned in the second phase of the hackathon. The only thing we wanted to improve on was the latency of the responses. We managed to reduce the latency of the product by about 60% by incorporating multithreading. We also worked on improving the UI a little bit.

Here is a YouTube link to the video of a simple demonstration for second Phase: [link](https://youtu.be/J3ALBBKPVKI)