## Test of V-network-graph V2 (fixed)
https://github.com/dash14/v-network-graph

### Test scope:
When hovering on a node or an edge of the graph 
highlight the closest nodes and connecting edges.
No such features out of the box.

### Demo:
https://codesandbox.io/s/github/chervontsev/vue-network-graph-test-v2-fixed/tree/main/?file=/src/App.vue

### Bugs:
When hovering on a graph node (in demo, whith checkbox checked) the whole graph twitches a little.
With bigger graph data this produces to many element redraws and cranks up CPU load.

### Resolved:
On hover, nodes and edges remain static.

### Issue link (closed):
https://github.com/dash14/v-network-graph/issues/56

### Initial version:
https://github.com/chervontsev/vue-network-graph-test-v1
https://codesandbox.io/s/github/chervontsev/vue-network-graph-test-v1/tree/main/?file=/src/App.vue

### Notes:
Seperate repos for demo purpases
