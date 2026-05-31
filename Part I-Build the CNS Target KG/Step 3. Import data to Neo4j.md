# Import data to Neo4j
```python
from neo4j import GraphDatabase

# link to your Neo4j AuraDB database
uri = "neo4j+s://you database ID.databases.neo4j.io"
username = "neo4j"
password = "your database password"

driver = GraphDatabase.driver(uri, auth=(username, password))

def create_nodes_and_relationships(tx, data):
    # Construct target node
    tx.run("""
    MERGE (t:Target {id: $target_id})
    SET t.name = $target_name,
        t.gene_symbol = $gene_symbol,
        t.function = $target_function,
        t.location = $location,
        t.family = $family
    """, target_id=data['target_id'], target_name=data['target_name'],
       gene_symbol=data['gene_symbol'], target_function=data['target_function'],
       location=data['location'], family=data['family'])
```
    
    # Construct drug node
```python
    tx.run("""
    MERGE (d:Drug {id: $drug_id})
    SET d.name = $drug_name,
        d.type = $drug_type,
        d.status = $drug_status,
        d.indication = $indication
    """, drug_id=data['drug_id'], drug_name=data['drug_name'],
       drug_type=data['drug_type'], drug_status=data['drug_status'],
       indication=data['indication'])
```
    
    # Construct disease node
```python
    tx.run("""
    MERGE (dis:Disease {id: $disease_id})
    SET dis.name = $disease_name,
        dis.category = $disease_category,
        dis.prevalence = $prevalence
    """, disease_id=data['disease_id'], disease_name=data['disease_name'],
       disease_category=data['disease_category'], prevalence=data['prevalence'])
```
    
    # Construct pathway node
```python
    tx.run("""
    MERGE (p:Pathway {id: $pathway_id})
    SET p.name = $pathway_name,
        p.description = $pathway_description
    """, pathway_id=data['pathway_id'], pathway_name=data['pathway_name'],
       pathway_description=data['pathway_description'])
```
    
    # Construct relationship
```python
    tx.run("""
    MATCH (d:Drug {id: $drug_id}), (t:Target {id: $target_id})
    MERGE (d)-[:TARGETS]->(t)
    """, drug_id=data['drug_id'], target_id=data['target_id'])
    
    tx.run("""
    MATCH (t:Target {id: $target_id}), (dis:Disease {id: $disease_id})
    MERGE (t)-[:ASSOCIATED_WITH]->(dis)
    """, target_id=data['target_id'], disease_id=data['disease_id'])
    
    tx.run("""
    MATCH (t:Target {id: $target_id}), (p:Pathway {id: $pathway_id})
    MERGE (t)-[:INVOLVED_IN]->(p)
    """, target_id=data['target_id'], pathway_id=data['pathway_id'])
```

# Import data in batch
```python
with driver.session() as session:
    for _, row in df.iterrows():
        session.execute_write(create_nodes_and_relationships, row.to_dict())

print("Knowledge Graph Construction Completed！")
```

