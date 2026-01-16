class Solution {
public:
    int minMen(vector<int>& arr) {
        int n = arr.size();
        
        // max_reach[i] stores the furthest index reachable by a worker 
        // whose coverage interval starts at index i.
        vector<int> max_reach(n, -1);

        // 1. Convert worker ranges into intervals and store max reach
        for (int i = 0; i < n; ++i) {
            if (arr[i] == -1) continue;
            
            // Calculate coverage range [left, right]
            int left = max(0, i - arr[i]);
            int right = min(n - 1, i + arr[i]);
            
            // We only care about the worker who can reach the furthest from 'left'
            max_reach[left] = max(max_reach[left], right);
        }

        // If no one can cover the start of the day (index 0), it's impossible
        if (max_reach[0] == -1) return -1;

        int workers = 0;
        int curr_end = 0;  // The end of the current worker's coverage
        int next_end = 0;  // The furthest point we can reach by adding one more worker

        // 2. Greedy scan (similar to Jump Game II)
        for (int i = 0; i < n; ++i) {
            // Update the furthest reach available within the current range
            if (max_reach[i] != -1) {
                next_end = max(next_end, max_reach[i]);
            }

            // If we reached the limit of the current worker
            if (i == curr_end) {
                workers++;          // We must hire a new worker
                curr_end = next_end; // Extend our coverage
                
                // Optimization: If we covered the whole day, stop early
                if (curr_end >= n - 1) return workers;
                
                // If we are stuck (cannot extend further than current position)
                if (i >= next_end) return -1;
            }
        }

        return -1;
    }
};
