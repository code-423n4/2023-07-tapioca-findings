I reviewed only yield strategies part. The overall impression is that the code wasn't fully tested for corner cases, and, more importantly, can use some generalisation: currently the strategies look like independent scripts, while, being many in number and a part of a bigger project, their logic can benefit from unification and the introduction of the pre and post operation invariants (that is, what should hold all the time before and after external pool interaction and the corresponding internal state changes).

### Time spent:
15 hours