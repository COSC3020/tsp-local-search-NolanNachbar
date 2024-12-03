# Traveling Salesperson Problem -- Local Search

This exercise is about the Traveling Salesperson Problem I mentioned in the
lecture on NP-hard problems -- given a set of cities, determine the length of
the shortest tour that visits all of them. We can get from any city to any other
city, i.e. the graph of cities is completely connected. We consider the version
of the Traveling Salesperson Problem that finds the shortest tour to visit $n$
cities, starting at a city and ending at the $n$ th city; it *does not* go
back to the start. The start city may be any of the cities. Remember that the
graph for a TSP is undirected, i.e. the cost is the same in either direction.

The 2-opt algorithm for solving the Traveling Salesperson Problem is a
randomized local search algorithm that, at each iteration, reverses part of the
route. It starts with a random route (this is the randomized part), and changes
part of the route in each step (this is the local search part, sprinkled with
more randomness). The pseudocode for one iteration is as follows:

```javascript
2optSwap(route, i, k)
  cities 1 to i-1 stay in the order they are
  cities i to k are reversed
  cities k + 1 to n stay in the order they are
```

For example, if I call the above function with route A--B--C--D--E--F, $i=2$,
$k=4$, the resulting route is A--B--E--D--C--F.

The algorithm starts with a random route; if the new route at the end of an
iteration decreases the total length, it is retained as the current incumbent.
The incumbent after the final iteration is returned as the solution.

Implement the 2-opt algorithm, which repeatedly runs the above steps. Your
implementation needs to fix two design parameters that I have left open. First,
you need to design a stopping criterion -- when would it make sense to stop and
return the shortest route found so far rather than trying another iteration?
Second, design a way to choose $i$ and $k$ -- note that they need to be
different in subsequent iterations, as one iteration would simply undo what
the previous one did otherwise. Start with the template I provided in `code.js`.
Describe in your code how you designed your stopping criterion and ways of
choosing $i$ and $k$ and why.

The function takes a distance matrix (the adjacency matrix for the graph where
the values in the cells are the distances between the corresponding cities) and
returns the length of the shortest tour (not the tour itself).

Test your new function; I've provided some basic testing code in `code.test.js`.

## Runtime Analysis

What is the worst-case asymptotic time complexity of your implementation? What
is the worst-case asymptotic memory complexity? Add your answer, including your
reasoning, to this markdown file.

Recall my code,
```js
function tsp_ls(distance_matrix) {
  let n = distance_matrix.length;
  let minRoute = Array.from({ length: n }, (v, i) => i).sort((a, b) => 0.5 - Math.random());// This is memory complexity of O(n)
  let minCost = calcCost(minRoute, distance_matrix); // This is time complexity of O(n) and memory complexity of O(1)
  let improving = true;
  // In the worst case, this is time complexity of \Theta(n!) because it'll loop through every possible permuation in the order of worst to best 
  while(improving) { 
    let routePath = [...minRoute]; // Get a shallow copy of min route O(n) 
    let r = Math.floor(Math.random() * (n - 1)) + 1; // Random Number in the range 1 to n - 1
    let l = Math.floor(Math.random() * (n - r - 1)) + (r + 1); // Random number in the range r + 1 to n
    swap(routePath, r, l);// This is time complexity of \Theta(n)
    let newCost = calcCost(routePath, distance_matrix); // This is O(n) time complexity
    if (newCost < minCost) {
      minCost = newCost;
      minRoute = routePath;
      improving = true;
    }
    else improving = false; // no improvement
  }
  return minCost;
}
 function calcCost(route, distance_matrix){ // This is time complexity of \Theta(n)
    let newCost = 0;
    for (let i = 0; i < distance_matrix.length - 1; i++) {
      newCost += distance_matrix[route[i]][route[i + 1]];
    }
    return newCost;
 }
function swap(arr, r, l) { // This is time complexity of \Theta(n)
  while (r < l) {
    let temp = arr[r];
    arr[r] = arr[l];
    arr[l] = temp;
    r++;
    l--;
  }
}
```
For the time complexity, note that the while loop terminates when no improvement is found in a single iteration. In the worst case, it may explore all possible permuations of cities,  which is $\Theta(n!)$. Also note that each iteration involves calculating the cost of the route O(n) and performing a swap O(n). 

Now adding those up:
- Time complexity of $\Theta((n + n! * (n + n))) \in \Theta(n * n!)$
- Memory complexity of $\Theta(n + n)) \in \Theta(n)$

I wrote this independently but I did have to look up some syntax. I also talked after class about how to decide when to stop swapping.

https://www.w3schools.com/js/js_sets.asp

https://stackoverflow.com/questions/5765398/whats-the-best-way-to-convert-a-number-to-a-string-in-javascript

https://stackoverflow.com/questions/2450954/how-to-randomize-shuffle-a-javascript-array

I certify that I have listed all sources used to complete this exercise, including the use of any Large Language Models. All of the work is my own, except where stated otherwise. I am aware that plagiarism carries severe penalties and that if plagiarism is suspected, charges may be filed against me without prior notice.
