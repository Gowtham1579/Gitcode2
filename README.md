# Gitcode2
#include <iostream>
#include <vector>

using namespace std;

const int MAXN = 100005;

vector<int> adj[MAXN];
int low[MAXN], disc[MAXN];
bool visited[MAXN];
vector<vector<int>> bridges;

void tarjan(int u, int parent) {
    static int time = 0;
    disc[u] = low[u] = ++time;
    visited[u] = true;

    for (int v : adj[u]) {
        if (!visited[v]) {
            tarjan(v, u);
            low[u] = min(low[u], low[v]);
            if (low[v] > disc[u]) {
                bridges.push_back({u, v});
            }
        } else if (v != parent) {
            low[u] = min(low[u], disc[v]);
        }
    }
}

vector<vector<int>> findBridges(int n, vector<vector<int>>& connections) {
    for (int i = 0; i < n; i++) {
        adj[i].clear();
        visited[i] = false;
    }
    bridges.clear();

    for (auto& conn : connections) {
        int u = conn[0], v = conn[1];
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            tarjan(i, -1);
        }
    }

    return bridges;
}

vector<vector<int>> findCriticalConnections(int n, vector<vector<int>>& connections) {
    vector<vector<int>> critical;

    vector<vector<int>> bridges = findBridges(n, connections);

    for (auto& bridge : bridges) {
        critical.push_back(bridge);
    }

    return critical;
}

int main() {
    int n = 4;
    vector<vector<int>> connections = {{0,1},{1,2},{2,0},{1,3}};

    vector<vector<int>> critical = findCriticalConnections(n, connections);

    for (auto& conn : critical) {
        cout << conn[0] << " " << conn[1] << endl;
    }

    return 0;
}
