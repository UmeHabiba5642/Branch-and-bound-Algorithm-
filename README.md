# Branch-and-bound-Algorithm-
#include <iostream>
#include <vector>
#include <cmath>
#include <climits>

using namespace std;

class TSP {
public:
    int n;                      // Number of cities
    vector<vector<int>> dist_matrix;  // Distance matrix
    int final_res = INT_MAX;     // Variable to store the minimum cost
    vector<int> final_path;      // Stores the optimal path

    TSP(vector<vector<int>>& matrix) {
        dist_matrix = matrix;
        n = dist_matrix.size();
    }

    // Function to calculate the lower bound for a given path
    int bound(vector<int>& curr_path, int curr_cost) {
        int count = curr_path.size();
        if (count == n) {
            return curr_cost + dist_matrix[curr_path[count - 1]][curr_path[0]];  // Returning to the start city
        }

        int lower_bound = curr_cost;
        
        // Compute the minimum cost for the remaining cities
        for (int i = 0; i < count; i++) {
            int min_edge = INT_MAX;
            for (int j = 0; j < n; j++) {
                if (dist_matrix[curr_path[i]][j] != 0) {
                    min_edge = min(min_edge, dist_matrix[curr_path[i]][j]);
                }
            }
            lower_bound += min_edge;
        }

        return lower_bound;
    }

    // Recursive function to solve the TSP using Branch and Bound
    int tsp(vector<int>& curr_path, int curr_cost, int level) {
        // If all cities have been visited, return to the starting city
        if (level == n) {
            return curr_cost + dist_matrix[curr_path[level - 1]][curr_path[0]];  // Returning to the start city
        }

        int lower_bound = bound(curr_path, curr_cost);
        
        if (lower_bound < final_res) {  // If the bound is less than the best result, explore this path
            for (int i = 0; i < n; i++) {
                // If the city has not been visited yet
                if (find(curr_path.begin(), curr_path.end(), i) == curr_path.end()) {
                    curr_path.push_back(i);
                    int new_cost = curr_cost + dist_matrix[curr_path[level - 1]][i];
                    int result = tsp(curr_path, new_cost, level + 1);
                    if (result < final_res) {
                        final_res = result;
                        final_path = curr_path;
                    }
                    curr_path.pop_back();
                }
            }
        }
        return final_res;
    }

    int solve() {
        vector<int> curr_path = {0};  // Starting from city 0
        return tsp(curr_path, 0, 1);
    }
};

int main() {
    // Example distance matrix (dist_matrix[i][j] is the distance between city i and city j)
    vector<vector<int>> dist_matrix = {
        {0, 10, 15, 20, 25},
        {10, 0, 35, 25, 30},
        {15, 35, 0, 30, 5},
        {20, 25, 30, 0, 10},
        {25, 30, 5, 10, 0}
    };

    TSP tsp(dist_matrix);
    int result = tsp.solve();

    cout << "Minimum cost: " << result << endl;
    cout << "Optimal path: ";
    for (int city : tsp.final_path) {
        cout << city << " ";
    }
    cout << endl;

    return 0;
}
