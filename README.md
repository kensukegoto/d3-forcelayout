# 飲み込めていないメソッド

```
- forcelayoutの各設定項目
- linksメソッドとは？
- d3.event.activeとは？
- alphaTargetとは？
```

# forcelayoutの各設定項目

```js
const simulation = d3.forceSimulation()
  .force("center",d3.forceCenter(width/2,height/2))
  .force("charge",d3.forceManyBody().strength(-50))
  .force("collide",d3.forceCollide(10).strength(0.9))
  .force("link",d3.forceLink().id(d => d.id))
```

# linksメソッドとは？

```js
simulation.force("link")
  .links(graph.links)
```

# d3.event.activeとは？

```js
d3.event.active
```

# alphaTargetとは？

```js
simulation.alphaTarget(.7).restart();

simulation.alphaTarget(0);
```