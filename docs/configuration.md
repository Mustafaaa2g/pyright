import networkx as nx
import random
import numpy as np
import matplotlib.pyplot as plt
from sklearn.ensemble import RandomForestClassifier

# Create a network topology with 20 users
def create_topology(num_users=20):
    graph = nx.Graph()
    switches = [f"Switch{i+1}" for i in range(3)]  # Create 3 switches
    hosts = [f"Host{i+1}" for i in range(num_users)]  # Create 20 hosts
    graph.add_nodes_from(switches + hosts)
    
    # Connect switches to each other
    graph.add_edges_from([
        ("Switch1", "Switch2"), 
        ("Switch2", "Switch3"), 
        ("Switch1", "Switch3")
    ])
    
    # Connect each host to a switch
    for i in range(num_users):
        graph.add_edge(f"Host{i+1}", random.choice(switches))
    
    return graph

# Train a more complex ML model
def train_ml_model():
    # Dummy data: features and labels
    # Features: [traffic volume, latency, packet loss]
    X = [
        [10, 5, 0],  # Low priority
        [50, 20, 1], # Medium priority
        [100, 30, 2], # High priority
        [5, 1, 0],   # Low priority
        [70, 25, 1], # Medium priority
        [120, 35, 3] # High priority
    ]
    y = [0, 1, 2, 0, 1, 2]  # Labels: 0 = low, 1 = medium, 2 = high
    model = RandomForestClassifier()
    model.fit(X, y)
    return model

# Simulate SDN decision-making
def simulate_network(graph, model):
    decisions = []
    for edge in graph.edges:
        # Simulate traffic conditions
        traffic_volume = random.randint(1, 150)  # Simulated traffic volume
        latency = random.randint(1, 50)          # Simulated latency
        packet_loss = random.randint(0, 5)       # Simulated packet loss

        # Create feature vector
        traffic_condition = [traffic_volume, latency, packet_loss]
        decision = model.predict([traffic_condition])[0]
        priority = ['Low', 'Medium', 'High'][decision]
        
        # Store the decision
        decisions.append((edge, traffic_condition, priority))
    
    return decisions

# Main
topology = create_topology(num_users=20)
ml_model = train_ml_model()
decisions = simulate_network(topology, ml_model)

# Print the decisions with priorities
print("Traffic Priorities for Each Edge:")
for edge, traffic_condition, priority in decisions:
    print(f"Edge {edge}: Traffic Volume: {traffic_condition[0]}, Latency: {traffic_condition[1]}, Packet Loss: {traffic_condition[2]}, Priority: {priority}")

# Visualization
plt.figure(figsize=(12, 8))
pos = nx.spring_layout(topology)  # positions for all nodes
nx.draw(topology, pos, with_labels=True, node_size=700, node_color='lightblue', font_size=10, font_weight='bold')
plt.title("Network Topology with 20 Users")
plt.show()
