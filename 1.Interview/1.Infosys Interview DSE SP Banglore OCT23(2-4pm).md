# Infosys SP/DSE Interview Experience – 2026 Graduate

My interview experience for the **Infosys Specialist Programmer (SP) / Digital Specialist Engineer (DSE) combined recruitment process**.

## Interview Overview

| Detail | Information |
|---|---|
| Company | Infosys |
| Recruitment Process | Combined SP/DSE |
| Candidate Profile | Fresher – 2026 Graduate |
| Interview Duration | Approximately 2 hours |
| Interview Timing | 2:00 PM – 4:00 PM |
| Coding Platform | Wingspan Portal |
| Main Topics | DSA, Java, SQL, System Design, Microservices, AI |

---

## 1. Coding Assessment – Wingspan Portal

The interviewer first reviewed my resume and noticed that I had mentioned solving 700+ DSA problems. I was asked to solve two coding questions on the Wingspan portal within 45 minutes.

### Question 1: Array Multiplication with Overflow Handling

The problem involved multiplying array elements according to a specified operation while handling overflow using modulo \(10^9\).

- I solved the problem completely.
- I explained my approach to the interviewer.
- The interviewer described the solution as simple during the subsequent discussion.

### Question 2: Frequency of Bit Lengths

The problem involved counting the number of bits required to represent each array element, finding the frequency of each bit length, and returning the minimum original element if a bit length occurred more than half the array size.

**My approach:**

1. Used a `while` loop to count the bits in each number by repeatedly dividing it by 2.
2. Stored the bit count of every element in an array.
3. Used a `HashMap` to calculate the frequency of each bit length.
4. Found the maximum frequency and its corresponding bit length.
5. Found the minimum original array element with that bit length.

**Result:** The public test cases passed, but the hidden test cases failed.

The interviewer asked me to dry-run both solutions. I explained my approach to the second problem, and we discussed the hidden-test failures.

---

## 2. Technical Interview

### A. DSA and Graph Algorithms

The interviewer asked me to introduce myself and then gave me algorithmic problems to solve using pen and paper.

#### Question 1: Lowest Common Ancestor (LCA) of a Binary Tree

- Wrote the solution on paper.
- Explained the algorithm and dry run.

#### Question 2: Course Schedule I

- Implemented the solution on paper.
- Explained the approach and dry run.

#### Question 3: Connected Components in a Graph

- Explained the optimal approach.
- Walked through an example.
- Discussed time and space complexity.

The interviewer was satisfied with my explanations of these three problems.

He also asked which DSA topics I was familiar with. I mentioned graph algorithms, including:

- Dijkstra's algorithm
- Bellman–Ford algorithm
- Floyd–Warshall algorithm
- Prim's algorithm
- Kruskal's algorithm

### B. Resume and DSA Practice

The interviewer asked how many of the 700+ DSA problems I had solved using AI.

I explained that I had been practising DSA since after completing my BCA and that I focused on understanding the problem, developing the solution and writing the code myself.

He also asked where I had learned Spring Boot, microservices and Low-Level Design (LLD).

I explained that I had learned Spring Boot and microservices through a coaching institute in Hyderabad and studied LLD using YouTube and Telegram resources.

### C. SQL and Database Design

**Questions asked:**

1. Find the fifth-highest salary.
2. Design database tables for orders and products in an e-commerce application.

For the fifth-highest salary, I provided two approaches:

- `LIMIT` with `OFFSET`
- `DENSE_RANK()`

During the database-design discussion, I initially missed the relationship needed to store multiple products in a single order.

When the interviewer asked how I would implement it, I proposed a separate mapping table containing `order_id` and `product_id`.

### D. JWT and System Design

The interviewer asked questions about the following topics:

- JWT and its implementation in my project.
- Handling concurrent requests when multiple users try to book the same ticket.
- Handling service failures at the API Gateway level.
- JWT revocation mechanisms.
- Rate limiting in my AI Resume Analyzer project.

**My answers:**

- **Concurrent ticket booking:** Suggested optimistic locking and atomic database operations.
- **Service failures:** Discussed the circuit breaker pattern.
- **JWT revocation:** Explained access tokens and refresh tokens while acknowledging that I had not implemented a revocation mechanism in my project.
- **Rate limiting:** Explained fixed-window, sliding-window, token-bucket and leaky-bucket algorithms. I clarified that I had not implemented rate limiting in my project.

### E. AI and Project Discussion

The interviewer asked whether my three projects were individual or team projects. I explained that they were my personal projects.

The AI-related questions included:

- What is Retrieval-Augmented Generation (RAG)?
- What are AI hallucinations?
- RAG vs. fine-tuning.
- Rate limiting in an AI Resume Analyzer.

I explained the concepts and how retrieved information can be provided as context to an LLM to help generate relevant answers.

---

## 3. Final Discussion and Feedback

The interviewer asked whether I could solve a random LeetCode Hard problem.

I said I was confident in attempting Medium-level problems and would try to solve a Hard problem as well.

When asked how many Medium problems I had solved, I estimated around 400–500.

At the end, I asked the interviewer where I could improve.

He told me that my profile was good and matched the requirements. He advised me to continue practising DSA without stopping and to keep learning, particularly about AI.

The interview concluded with a brief, friendly conversation about my name and hometown.

---

## 4. Overall Experience

The interview covered a wide range of topics:

- Data Structures and Algorithms
- Trees and Graphs
- SQL and Database Design
- Java Backend Development
- JWT and Security
- Microservices and API Gateway
- System Design
- Rate Limiting
- RAG and AI Fundamentals
- Resume and Project Discussion

The coding assessment was challenging because the second question passed public test cases but failed hidden tests.

During the technical interview, I was able to solve three DSA problems on paper and explain the approaches and complexity.

Overall, it was a comprehensive technical interview that helped me identify areas to improve, particularly database relationships, service resilience and implementation-level system design.

## 5. Preparation Tips for Future Candidates

Based on my experience, I recommend preparing the following:

- Practise DSA problems on paper, not only in an IDE.
- Be comfortable explaining dry runs and time and space complexity.
- Revise tree and graph algorithms, including topological sorting and connected components.
- Practise SQL queries involving ranking, joins and aggregation.
- Understand every feature and design decision in your resume projects.
- Prepare for follow-up questions on database relationships and concurrency.
- Revise JWT, Spring Security, microservices, circuit breakers and rate limiting.
- Prepare basic AI concepts, especially RAG, hallucinations and fine-tuning.
- Be honest about what you have implemented and what you know conceptually.

---

**Disclaimer:** This is my personal interview experience. Questions and evaluation criteria may vary across candidates and recruitment cycles.

If this experience helps you, feel free to use it for preparation. All the best to everyone preparing for Infosys SP/DSE!
