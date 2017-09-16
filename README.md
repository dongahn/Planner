### PLANNER API

Planner provides a simple abstraction and efficient mechanisms
to keep track of the resource states for batch-job scheduling.
Its API builds on two simple concepts:

- Planner: just like your calendar planner, a planner object
allows you to update and query the available resource states over
time (i.e., integer unit-less time). 

- Span: just like you mark your activities over consecutive days
in your calendar planner with a span, a span
allows you to update your resource uses using a start time,
duration and resource requirement.

Planner is designed for speed. Specifically, 
it uses Linux OS kernel's red-black binary search tree code
to update and query the resource states over time.
It manages each of the two scheduled points of spans
using two highly efficient binary search trees:

- Scheduled point red-black binary search tree
- Min-time resource augmented red-black search tree

The scheduled-point search tree allows the planner to find the
accurate resource state of any given instant time with
(`O(log n)`) complexity.
On the other hand, the min-time resource tree enables you to find
the earliest scheduable point where your resource
requirement can be satisfied also with (`O(log n)`)
complexity. Using these search trees, you
can efficiently operate on the planner over
both the time and resource dimensions alike.

Planner was born out of real-world needs in Flux's
batch-job scheduling infrastructure. As high performance
computing (HPC) is undergoing significant changes
in both performance-limiting resource types
and workloads, simple data models
(e.g., bitmaps) representing HPC compute and other
consumable resources present increasingly greater
challenges for effective scheduling. In response,
Flux is in the process of modeling complex HPC
resources using a graph. While this
allows a highly sophisticated resource selections,
it can be done at the exchange of excessive, highly
time-consuming searches of a large graph.

Planner's efficient mechanism is specifically designed
to mitigate the scalability and performance risk
of such a graph-based scheduling approach. More specifically,
it enables a novel scheme called scheduler-driven
aggregate updates.

As part of scheduling update activities (i.e., update
for an allocation or reservation), the scheduler will
update all of the planner objects which were involved
in the allocation/reservation. This then allows each
of the resource vertices to retain the up-to-date
summary information on the vertices
at its sub-tree or through other relevant topological
connectivity.
This information then allows the scheduler to prune the
graph search for the the subsequent allocation
or reservation. 

