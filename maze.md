Designing a procedural algorithm for a container city requires a multi-layered approach. We'll use a grid-based system where each cell can be a container, a walkway, or empty space. We'll leverage a Markov Chain to determine the function of each container, and a randomized maze generation algorithm to create the physical layout of walls and doors.
Algorithm: Container City Generation
Step 1: Grid Initialization
 * Define Grid Dimensions: Set a 3D grid, say G(x, y, z), where x and y are the horizontal dimensions and z is the vertical. The size of each cell corresponds to a standard shipping container.
 * Initialize Grid: Fill the entire grid with a default "empty" state. This will be our building canvas.
 * Place Foundation Containers: Start by placing a foundation layer of containers, maybe at z=0, to establish a base footprint. These can be randomly placed to create an irregular, non-uniform ground level.
Step 2: Markov Chain for Container Use
This is the core of the "city" aspect. The Markov Chain will determine the function of each new container based on the function of its neighbors. This creates a sense of organic, clustered development (e.g., a "shop" is more likely to be next to another "shop" than a "residence").
 * Define States: Create a set of "states" or container uses:
   * S_1: Residence (small apartment)
   * S_2: Shop/Market Stall
   * S_3: Workshop/Industrial
   * S_4: Public Space (e.g., communal kitchen, lounge)
   * S_5: Utility/Storage
   * S_6: Walkway/Corridor (this state is for a "passageway container" with no internal walls)
 * Create a Transition Matrix: This matrix defines the probability of a container having a certain state, given the state of its neighbors. Let's call it M. M_{ij} is the probability of a container being of type j, given a neighboring container of type i. The columns of the matrix should sum to 1.
   * For example, M_{12} (Residency to Shop) might be high, as shops often serve residents.
   * M_{22} (Shop to Shop) might be very high to create commercial clusters.
   * M_{14} (Residency to Public Space) could be moderate, as people need communal areas.
   * M_{35} (Workshop to Utility) might be high, as industrial spaces need storage.
 * Iterative Placement:
   * Seed the City: Place one or more initial containers with a random state (e.g., a central "market" container).
   * Grow the City: Iterate through the grid. For each empty cell adjacent to an existing container, calculate the probabilities for its state.
   * Calculate New State Probability: The probability of a new container at position (x, y, z) having state S_j is the sum of probabilities influenced by all its existing neighbors.
     P(S_j) = \sum_{i \in \text{neighbors}} M_{S_i, S_j}
     To make this work, you'd normalize these sums so they add up to 1.
   * Place and Assign: Select a new state S_{new} for the container based on these weighted probabilities and place the container.
   * Continue: Repeat this process until the desired city size is reached. You can also introduce a small chance for a "random" container to be placed, preventing the city from being too predictable.
Step 3: Maze Generation for Walls and Doors
This step takes the city's container layout and transforms it into a navigable, claustrophobic maze. This happens on a per-container basis.
 * Per-Container Grid: For each container cell assigned a state (e.g., Residence, Shop), create a smaller, internal grid, say G_{int}(a, b), where a and b are small dimensions (e.g., 4 \times 8).
 * Maze Algorithm Selection: A suitable algorithm is a randomized version of Recursive Division or Prim's Algorithm. These are "wall-adding" or "passage-carving" algorithms that are great for creating complex, winding paths.
   * Recursive Division: Start with a container with no internal walls. Randomly place a vertical or horizontal wall with a single passage (door) in it. Recursively apply this to the two new sub-chambers until the sub-chambers are too small to divide. This creates a "perfect" maze with no loops.

* Prim's Algorithm: Start with a grid of solid walls. Pick a starting cell. Add its walls to a list. While the list isn't empty, pick a random wall. If that wall divides two cells in different parts of the maze, remove the wall, add the new cell's walls to the list, and merge the two parts. This creates more "spacious" mazes with a single solution.
 * Integrate Doors and Walls:
   * Internal Walls: The algorithm's "walls" become the interior walls of the container. These walls can be simple wooden or metal partitions.
   * Doors: The "passages" or "removed walls" in the maze algorithm become the internal doors.
   * External Walls: The outer boundary of the container's internal grid represents the external walls. However, we need to create doors between containers to connect the city.
 * Connecting the City:
   * After generating the internal maze for a container, check its adjacent containers.
   * If a neighbor at (x+1, y, z) exists, randomly decide if a door should be placed in the shared wall. The probability of a door could be influenced by the container states (e.g., high probability between two shops, low between a residence and a workshop).
   * If a door is placed, carve an opening in the external wall of both containers at that shared boundary.
   * Repeat for all six sides of each container.
Final Output of the Algorithm:
The output for the 3D generation model would be a data structure containing:
 * A list of all container positions in the 3D grid.
 * For each container, a "type" or "state" (e.g., "Residence," "Shop").
 * For each container, an internal grid of wall and door locations, essentially a blueprint for its interior.
 * A list of "external door" connections between containers.
This combined algorithm creates a procedurally generated container city that is not only a physical structure but also a semi-believable, functional (and confusing) urban environment.