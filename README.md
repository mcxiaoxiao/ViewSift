# ViewSift

**ViewSift** is a powerful algorithm designed to select interconnected views from pre-screened user-defined views, optimizing for maximum combined score. This tool is ideal for identifying reliable data relationships in complex database schemas. One example use case is selecting views for power supply analysis.

## Purpose

The primary goal of ViewSift is to help users optimize pre-selected sets of views, such as rerankers or model outputs, by analyzing their logical relationships within an SQLite database. By understanding the relationships between views through their base tables, ViewSift aids in optimizing database queries, understanding data dependencies, and improving LLM/Other models input.

## Algorithm Overview

ViewSift consists of three main funcs:

1. **get_base_tables(cursor, view_name)**: Retrieves the base tables used by a given view.
2. **find_related_views(views)**: Identifies pairs of views that share common base tables.
3. **find_connected_components(views, related_views)**: Finds connected components of views using depth-first search (DFS).

## Input and Output Examples

### Input

1. **Views**: A list of view names in the SQLite database.
2. **Cursor**: An SQLite cursor object to execute SQL queries.
3. **View Scores**: A dictionary of view scores.

### Example

#### Input 
```python
views = [
    'SubstationView', 'TransformerView', 'TransformerTypeView',
    'MaintenanceAnalysisView', 'PowerConsumptionView',
    'SubstationMaintenanceView', 'SubstationPerformanceView'
]

view_scores = {
    'SubstationView': 1.5,
    'TransformerView': 2.0,
    'TransformerTypeView': 0.8,
    'MaintenanceAnalysisView': 1.2,
    'PowerConsumptionView': 0.5,
    'SubstationMaintenanceView': 2.5,
    'SubstationPerformanceView': 3.0
}
cursor = sqlite3.connect('database.db').cursor()
```

#### Output (Related, and their cumulative score of 8.0 > 0.5)
```python
Maximum Connected Component: 
['SubstationView', 'SubstationMaintenanceView', 'MaintenanceAnalysisView', 'TransformerView', 'TransformerTypeView'] 
Total Score: 8.0
```

#### Input
```python
views = [
    'SubstationView', 'TransformerView', 'TransformerTypeView',
    'MaintenanceAnalysisView', 'PowerConsumptionView',
    'SubstationMaintenanceView', 'SubstationPerformanceView'
]

view_scores = {
    'SubstationView': 1.5,
    'TransformerView': 2.0,
    'TransformerTypeView': 0.8,
    'MaintenanceAnalysisView': 1.2,
    'PowerConsumptionView': 10.5,
    'SubstationMaintenanceView': 2.5,
    'SubstationPerformanceView': 3.0
}
cursor = sqlite3.connect('database.db').cursor()
```

#### Output (Although independent, have a cumulative score of 10.5 > 8.0)
```python
Maximum Connected Component: 
['PowerConsumptionView'] 
Total Score: 10.5
```