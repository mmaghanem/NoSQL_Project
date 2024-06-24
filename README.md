# Acme Gourmet Meals (AGM) - Optimizing Delivery Systems with NoSQL Databases and Graph Algorithms

Welcome to the repository for the Acme Gourmet Meals (AGM) project! This project focuses on enhancing the delivery systems of AGM by leveraging NoSQL databases and advanced graph algorithms. The goal is to improve operational efficiency by integrating Neo4j for route optimization and MongoDB and Redis for managing large-scale data.

## Table of Contents

1. [Introduction](#introduction)
2. [Project Components](#project-components)
3. [Methods and Techniques](#methods-and-techniques)
4. [Skills and Technologies Applied](#skills-and-technologies-applied)
5. [Benefits of the Project](#benefits-of-the-project)

## Introduction

In this project, I designed NoSQL databases and graph algorithms to optimize delivery routes and manage data for Acme Gourmet Meals. The work emphasizes the use of Python for data manipulation, analysis, and the implementation of graph-based algorithms to solve complex business problems.

## Project Components

The project is divided into several key components:

1. **Neo4j for Route Optimization**:
   - Implementing graph algorithms such as Shortest Path, Harmonic Centrality, and Minimum Spanning Tree to optimize delivery routes.
   - Utilizing Neo4j's capabilities to analyze complex relationships between delivery locations and public transportation nodes (BART stations).

2. **Data Management with MongoDB and Redis**:
   - Using MongoDB to handle large volumes of unstructured data related to customer orders and known traffic issues.
   - Leveraging Redis for real-time data management, including tracking delivery vehicles and handling unpredictable traffic conditions.

3. **Python-Based Data Processing and Analysis**:
   - Extensive use of Python libraries for data cleaning, feature engineering, and applying machine learning models.
   - Visualizing the results to provide insights into the efficiency of different delivery strategies.

## Methods and Techniques

### Neo4j for Graph-Based Delivery Optimization

**Objective**:

Utilize Neo4j to implement and analyze graph algorithms that optimize delivery routes based on various factors such as distance, cost, and traffic conditions.

**Techniques**:

- **Shortest Path Algorithm**: Determines the most efficient route from the store to the customer using the BART system. 
  ```python
  from neo4j import GraphDatabase

  def find_shortest_path(driver, start, end):
      with driver.session() as session:
          result = session.run(
              "MATCH (start:Location {name: $start}), (end:Location {name: $end}), "
              "path = shortestPath((start)-[*]-(end)) "
              "RETURN path",
              start=start, end=end
          )
          return result.single()["path"]

  driver = GraphDatabase.driver(uri="bolt://localhost:7687", auth=("neo4j", "password"))
  path = find_shortest_path(driver, "Store", "Customer")
```

- **Harmonic Centrality:** Analyzes the centrality of nodes (BART stations) to understand which stations are most accessible and crucial for optimizing pickup locations.
```python
from neo4j import GraphDatabase

def calculate_harmonic_centrality(driver):
    with driver.session() as session:
        result = session.run(
            "CALL algo.closeness.harmonic.stream('Location', 'ROUTE') "
            "YIELD nodeId, centrality "
            "RETURN algo.getNodeById(nodeId).name AS name, centrality "
            "ORDER BY centrality DESC"
        )
        return result.data()

centrality_scores = calculate_harmonic_centrality(driver)
```

- **Minimum Spanning Tree:** Determines the least-cost paths to connect all delivery points efficiently.
```python
from neo4j import GraphDatabase

def minimum_spanning_tree(driver):
    with driver.session() as session:
        result = session.run(
            "CALL algo.spanningTree.minimum('Location', 'ROUTE', 'distance') "
            "YIELD writeMillis"
        )
        return result.data()

mst_results = minimum_spanning_tree(driver)
```

### Data Management with MongoDB and Redis
**Objective**:
Manage large-scale and real-time data using MongoDB for structured and unstructured data, and Redis for handling real-time updates and transient data.

**Techniques**:
- **MongoDB for Traffic Data:** Storing grid-based traffic information to optimize delivery routes by considering known traffic issues.
```python
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017/")
db = client["delivery_db"]
traffic_collection = db["traffic_data"]

# Insert traffic data
traffic_collection.insert_one({"grid_id": "A1", "issue": "construction", "severity": "high"})
```

- **Redis for Real-Time Tracking:** Using Redis to store and update the real-time location of delivery vehicles, allowing for dynamic route adjustments based on current traffic conditions.
```python
import redis

r = redis.Redis(host='localhost', port=6379, db=0)

# Set the initial location of a delivery vehicle
r.set('vehicle_1_location', 'lat:37.7749,lon:-122.4194')

# Update the location based on GPS data
r.set('vehicle_1_location', 'lat:37.7750,lon:-122.4195')
```

## Skills and Technologies Applied
### NoSQL Databases and Real-Time Data Management
- **Neo4j:** Applied graph algorithms to solve complex delivery optimization problems.
- **MongoDB:** Managed large-scale and unstructured data for traffic and delivery information.
- **Redis:** Handled real-time data updates and transient information for tracking delivery vehicles.

## Benefits of the Project
1. **Improved Delivery Efficiency:**
   - Optimizes delivery routes using advanced graph algorithms, reducing travel time and costs.
   - Enhances operational efficiency by integrating real-time data management.

2. **Scalable Data Management:**
   - Utilizes NoSQL databases to handle large volumes of data, providing flexibility and scalability.
   - Facilitates efficient storage and retrieval of complex data structures.
