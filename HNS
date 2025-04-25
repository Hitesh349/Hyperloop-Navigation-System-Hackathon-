#include <iostream>
#include <vector>
#include <cmath>
#include <queue>
#include <vector> // Included again, but harmless. Needed for priority_queue template
#include <limits> // For numeric_limits
#include <iomanip> // For setprecision
#include <unordered_set> // For maintenance stations lookup
#include <functional> // For std::greater
#include <string>     // For getline
#include <sstream>    // For istringstream

// Use the standard namespace
using namespace std;

// Structure to hold station coordinates
struct Point {
    int x, y, z;
};

// Function to calculate Euclidean distance in 3D
double euclidean_distance_3d(const Point& p1, const Point& p2) {
    double dx = static_cast<double>(p1.x) - p2.x; // Use double for calculations
    double dy = static_cast<double>(p1.y) - p2.y;
    double dz = static_cast<double>(p1.z) - p2.z;
    return sqrt(dx*dx + dy*dy + dz*dz);
}

// Type alias for pair<double, int> used in priority queue
using pdi = pair<double, int>;

int main() {
    // Faster I/O (optional but recommended for competitive programming)
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n, m;
    // Read N (number of stations) and M (number of tunnels)
    if (!(cin >> n >> m)) {
        cerr << "Error reading N and M" << endl;
        return 1;
    }

    if (n == 0) {
        cout << -1 << endl;
        // Consume potential remaining input lines if necessary
        string dummy_line;
        for(int i=0; i<m; ++i) getline(cin, dummy_line); // Consume tunnel lines if any were expected
        getline(cin, dummy_line); // Consume src/dest line
        getline(cin, dummy_line); // Consume maintenance list line
        return 0;
    }


    vector<Point> stations(n);
    // Read station coordinates
    for (int i = 0; i < n; ++i) {
        if (!(cin >> stations[i].x >> stations[i].y >> stations[i].z)) {
            cerr << "Error reading coordinates for station " << i << endl;
             return 1;
        }
    }

    // Adjacency list: stores pairs of {neighbor_index, cost}
    vector<vector<pair<int, double>>> adj(n);
    for (int i = 0; i < m; ++i) {
        int u, v, initial_cost_ignored;
        if (!(cin >> u >> v >> initial_cost_ignored)) {
             cerr << "Error reading tunnel information" << endl;
             return 1; // Or handle differently, e.g., skip tunnel
        }

        // --- Adjust for 0-based indexing if input is 1-based ---
        u--;
        v--;

        // Validate indices
        if (u < 0 || u >= n || v < 0 || v >= n) {
            cerr << "Warning: Invalid tunnel indices (" << u + 1 << ", " << v + 1 << ") ignored." << endl;
            continue; // Skip this invalid tunnel
        }

        // Calculate the actual cost (Euclidean distance)
        double cost = euclidean_distance_3d(stations[u], stations[v]);

        // Add bidirectional edges
        adj[u].push_back({v, cost});
        adj[v].push_back({u, cost});
    }

    int source, destination;
    // Read source and destination stations
    if (!(cin >> source >> destination)) {
         cerr << "Error reading source and destination" << endl;
         return 1;
    }

    // --- Adjust for 0-based indexing if input is 1-based ---
    source--;
    destination--;

    // Validate indices
    if (source < 0 || source >= n || destination < 0 || destination >= n) {
        cerr << "Invalid source or destination index." << endl;
        cout << -1 << endl;
        // Consume potential remaining input line if necessary
        string dummy_line;
        getline(cin >> ws, dummy_line); // Read maintenance line
        return 0;
    }


    // Read list of stations under maintenance
    unordered_set<int> maintenance_stations;
    string line;
    // Consume the rest of the line where source/dest were read,
    // then read the maintenance line
    getline(cin >> ws, line); // ws consumes leading whitespace
    if (!line.empty()) {
        stringstream ss(line);
        int idx;
        while (ss >> idx) {
            // --- Adjust for 0-based indexing if input is 1-based ---
            idx--;
            if (idx >= 0 && idx < n) {
                maintenance_stations.insert(idx);
            } else {
                cerr << "Warning: Invalid maintenance index " << idx + 1 << " ignored." << endl;
            }
        }
    }


    // --- Dijkstra's Algorithm ---

    // Check if source or destination is under maintenance
    if (maintenance_stations.count(source) || maintenance_stations.count(destination)) {
        cout << -1 << endl;
        return 0;
    }

    // Initialize distances: infinity for all, 0 for source
    vector<double> distances(n, numeric_limits<double>::infinity());
    distances[source] = 0.0;

    // Priority queue (min-heap): stores {current_distance, node_index}
    // Use std::greater to make it a min-heap based on the first element (distance)
    priority_queue<pdi, vector<pdi>, greater<pdi>> pq;
    pq.push({0.0, source});

    while (!pq.empty()) {
        double current_dist = pq.top().first;
        int u = pq.top().second;
        pq.pop();

        // If we found a shorter path already, skip (optimization)
        if (current_dist > distances[u]) {
            continue;
        }

        // If we reached the destination
        if (u == destination) {
            cout << fixed << setprecision(2) << distances[destination] << endl;
            return 0; // Found the shortest path
        }

        // Explore neighbors
        for (const auto& edge : adj[u]) {
            int v = edge.first;
            double weight = edge.second;

            // Skip neighbors that are under maintenance
            if (maintenance_stations.count(v)) {
                continue;
            }

            // Relaxation step
            double distance_through_u = current_dist + weight;
            if (distance_through_u < distances[v]) {
                distances[v] = distance_through_u;
                pq.push({distance_through_u, v});
            }
        }
    }

    // If the loop finishes and destination wasn't reached
    cout << -1 << endl;

    return 0;
} 
