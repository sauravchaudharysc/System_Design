# Brief History of Large Scale Distributed Computing and its Evolution

We created software applications and deployed them on computers. These applications needed to do a lot of things, including but not limited to storing and processing data. Over a period of time, the data volume kept growing. Hence, the requirement for storage and computational power also increased.

However, this need was being fulfilled by the rapid advancement in storage and compute hardware. But the advent and rapid adoption of the internet disrupted this balance. The scope and reach of software applications started to expand across geographic boundaries and covered the entire planet.

As a result, data began growing and evolving exponentially. It was neither possible nor practical for hardware technology alone to catch up with this growth and disruption. But we needed a solution. Google was not only the first to realize this problem but also the first to develop a viable solution and establish a commercially successful business around it.

At a high level, **Google** had four main problems to solve while creating a search engine:

**1. Data Collection and Ingestion**

The first thing they wanted to do was to discover and crawl web pages over the internet and capture their content and metadata.

**2. Data Storage and Management**

They knew they were going to collect a massive amount of data at a planet scale. So, the next problem was to store and manage hundreds of petabytes of data.

**3. Data Processing and Transformation**

The next challenge was to obtain the computational power required to process these massive volumes of data.

**4. Data Access and Retrieval**

Finally, the last problem was to organize and store the processed data so that it could be efficiently accessed and retrieved.

Keeping your **same flow**, here is the **final combined explanation**:



## **How Google Solved the Problem**

To handle the massive scale of the internet, Google did not depend on a single system. Instead, it built a **complete distributed ecosystem**, where each problem was solved using specialized distributed technologies.

At a high level, Google solved the four problems as follows:

**1. Data Collection and Ingestion**

- Google built **distributed web crawlers (Googlebot)**
- These crawlers:
  - Continuously scan the internet
  - Discover new and updated pages
  - Collect content and metadata
- The crawling process runs on **multiple machines in parallel**, allowing Google to cover the entire web efficiently

**2. Data Storage and Management**

- Google developed the **Google File System (GFS)**
- It:
  - Stores data across **clusters of machines**
  - Breaks data into **chunks**
  - Replicates data for **fault tolerance**
- This allowed Google to store **hundreds of petabytes of data reliably**

**3. Data Processing and Transformation**

- Google introduced **MapReduce**
- It:
  - Splits large computations into smaller tasks
  - Runs them in **parallel across machines**
  - Aggregates results efficiently
- This enabled processing of massive datasets like:
  - Web indexing
  - Ranking calculations

**4. Data Access and Retrieval**

- Google used:
  - **Bigtable** for structured storage
  - **PageRank** for ranking web pages
- It:
  - Organizes processed data into **indexes**
  - Uses PageRank to **rank pages based on importance**
  - Enables **fast and relevant search results**

### **PageRank Algorithm**

To solve the problem of **data access and retrieval**, Google needed a way to **rank web pages** so that the most relevant and important pages appear first in search results.

#### **Basic Idea**

- Not all web pages are equally important
- A page is considered important if:
  - Many other pages link to it
  - Important pages link to it

- The web is treated as a **directed graph**:
  - Nodes → Web pages
  - Edges → Hyperlinks
- PageRank assigns a **score (rank)** to each page based on:
  - Number of incoming links
  - Quality of those links

#### **PageRank Formula**

$$
PR(A) = \frac{1-d}{N} + d \sum_{i=1}^{k} \frac{PR(T_i)}{C(T_i)}
$$

**Where:**

- **PR(A)** → PageRank of page A
- **d** → Damping factor (usually ~0.85)
- **N** → Total number of pages
- **Tᵢ** → Pages linking to A
- **C(Tᵢ)** → Number of outgoing links from page Tᵢ

#### **Intuition**

- Each page distributes its importance equally among the pages it links to
- A page gets higher rank if:
  - It is linked by many pages
  - It is linked by high-ranking pages

#### **How It Works (Iterative Process)**

1. Assign initial rank to all pages (usually equal)
2. Recalculate ranks using the formula
3. Repeat multiple times
4. Converge to stable values

#### **Why Damping Factor?**

- Prevents infinite loops
- Models a user randomly jumping to another page
- Ensures convergence of the algorithm

**The open source community created HDFS and Hadoop MapReduce, inspired by Google’s GFS and MapReduce respectively.**