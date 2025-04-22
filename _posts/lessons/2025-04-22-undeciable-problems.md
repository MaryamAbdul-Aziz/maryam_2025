------
comments: true
layout: post
title: Undecidable Problems, Graphs, and Heuristics Hacks
type: hacks
courses: { compsci: {week: 30} }
------

## Hacks

### Research and write a brief explanation of another undecidable problem not discussed in class

- One example of an undeciable problem is the busy beaver problem. It asks the maximum number of steps a Turing machine with n states can execute before halting, if it halts at all. It’s undecidable because there’s no single, general algorithm that can determine whether Turing machines will halt. It is related to the halting problem

### Implement the Nearest Neighbor algorithm for the TSP in a programming language of your choice

JavaScript:

```javascript
function nearestNeighborTSP(distances, start = 0) {
    const n = distances.length;
    const unvisited = new Set([...Array(n).keys()]);
    unvisited.delete(start);
    const tour = [start];
    let current = start;
    let totalDistance = 0;

    while (unvisited.size > 0) {
        let nearest = null;
        let minDist = Infinity;

        for (let city of unvisited) {
            if (distances[current][city] < minDist) {
                minDist = distances[current][city];
                nearest = city;
            }
        }

        totalDistance += minDist;
        current = nearest;
        tour.push(current);
        unvisited.delete(current);
    }

    totalDistance += distances[current][start];
    tour.push(start);

    return { tour, totalDistance };
}
```

### Find a real-world example where heuristics are used and explain why an exact solution isn’t practical

Antivirus software uses heuristics. It checks files for viruses, then flags suspicious files if it suspects malware rather than checking for exact signals of viruses. This is much faster and efficient as new viruses are constantly being made and it isn't feasible to teach an antivirus program about every little variation. Heuristics are a more practical solution.