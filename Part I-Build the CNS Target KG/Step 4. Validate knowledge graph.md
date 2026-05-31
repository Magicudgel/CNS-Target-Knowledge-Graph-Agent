# Validate knowledge graph

## Search the first 10 targets
```python
with driver.session() as session:
    result = session.run("MATCH (t:Target) RETURN t.name, t.gene_symbol LIMIT 10")
    for record in result:
        print(f"{record['t.name']} ({record['t.gene_symbol']})")
```


## Search Dopamine D2 Receptor
```python
result = session.run("""
MATCH (t:Target {gene_symbol: 'DRD2'})-[:ASSOCIATED_WITH]->(d:Disease)
MATCH (t)-[:INVOLVED_IN]->(p:Pathway)
MATCH (drug:Drug)-[:TARGETS]->(t)
RETURN t.name, collect(DISTINCT d.name) as diseases, 
       collect(DISTINCT p.name) as pathways, 
       collect(DISTINCT drug.name) as drugs
""")
for record in result:
    print(f"Target: {record['t.name']}")
    print(f"relevant disease: {', '.join(record['diseases'])}")
    print(f"pathways involved: {', '.join(record['pathways'])}")
    print(f"targetting drugs: {', '.join(record['drugs'])}")
```
