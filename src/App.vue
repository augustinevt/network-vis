<template>
  <div id="app" @click="onClick">

    <svg xmlns="http://www.w3.org/2000/svg"
         :width="width+'px'"
         :height="height+'px'"
         @mousemove="drag($event)"
         @mouseup="drop()"
         v-if="bounds.minX">

      <line v-for="link in graph.links" :key="link.id"
            :x1="coords[link.source.index].x"
            :y1="coords[link.source.index].y"
            :x2="coords[link.target.index].x"
            :y2="coords[link.target.index].y"
            stroke="black" stroke-width="2"/>

      <circle v-for="(node, i) in graph.nodes" :key="node.id"
              :cx="coords[i].x"
              :cy="coords[i].y"
              :r="20" :fill="colors[Math.ceil(Math.sqrt(node.index))]"
              stroke="white" stroke-width="1"
              @mousedown="currentMove = {x: $event.screenX, y: $event.screenY, node: node}"/>

    </svg>
    <div> helloooooasdf </div>
  </div>
</template>

<script>

import * as d3 from 'd3'
import Graph from 'graph-utils'

const testAdjacencyList = {
  A: {id: 'A', edges: ["B"]},
  B: {id: 'B', edges: ["C"]},
  C: {id: 'C', edges: ["D"]},
  D: {id: 'D', edges: ["A"]}
}


export default {
  el: '#app',
  data: () => ({
    graph: new Graph({
      A: {x: null, y: null, id: 'A', index: 'A', edges: ["B"]},
      B: {x: null, y: null, id: 'B', index: 'B', edges: ["C"]},
      F: {x: null, y: null, id: 'F', index: 'B', edges: ["C"]},
      H: {x: null, y: null, id: 'H', index: 'B', edges: ["C"]},
      R: {x: null, y: null, id: 'R', index: 'B', edges: ["C"]},
      N: {x: null, y: null, id: 'N', index: 'B', edges: ["C"]},
      M: {x: null, y: null, id: 'M', index: 'B', edges: ["C"]},
      C: {x: null, y: null, id: 'C', index: 'C', edges: ["D"]},
      D: {x: null, y: null, id: 'D', index: 'D', edges: ["A"]}}).convertToArrays(),
    width: Math.max(document.documentElement.clientWidth, window.innerWidth || 0),
    height: Math.max(document.documentElement.clientHeight, window.innerHeight || 0) - 40,
    padding: 20,
    colors: ['#2196F3', '#E91E63', '#7E57C2', '#009688', '#00BCD4', '#EF6C00', '#4CAF50', '#FF9800', '#F44336', '#CDDC39', '#9C27B0'],
    simulation: null,
    currentMove: null
  }),
  computed: {
    bounds() {
      return {
        minX: Math.min(...this.graph.nodes.map(n => n.x)),
        maxX: Math.max(...this.graph.nodes.map(n => n.x)),
        minY: Math.min(...this.graph.nodes.map(n => n.y)),
        maxY: Math.max(...this.graph.nodes.map(n => n.y))
      }
    },
    coords() {
      return this.graph.nodes.map(node => {
        return {
          x: this.padding + (node.x - this.bounds.minX) * (this.width - 2*this.padding) / (this.bounds.maxX - this.bounds.minX),
          y: this.padding + (node.y - this.bounds.minY) * (this.height - 2*this.padding) / (this.bounds.maxY - this.bounds.minY)
        }
      })
    }
  },
  created(){
     this.simulation = d3.forceSimulation(this.graph.nodes)
      //eslint:disable-next-line
        .force('charge', d3.forceManyBody())
        .force('link', d3.forceLink(this.graph.links).id(d => d.id))
        .force("center", d3.forceCenter(this.width / 2, this.height / 2));
        // .force('x', d3.forceX())
        // .force('y', d3.forceY())
  },
  methods: {
    drag(e) {
      if (this.currentMove) {
        this.currentMove.node.fx = this.currentMove.node.x - (this.currentMove.x - e.screenX) * (this.bounds.maxX - this.bounds.minX) / (this.width - 2 * this.padding)
        this.currentMove.node.fy = this.currentMove.node.y -(this.currentMove.y - e.screenY) * (this.bounds.maxY - this.bounds.minY) / (this.height - 2 * this.padding)
        this.currentMove.x = e.screenX
        this.currentMove.y = e.screenY
      }
    },
    onClick() {
      this.graph = new Graph({
        A: {x: null, y: null, id: 'A', edges: ["B"]},
        B: {x: null, y: null, id: 'B', edges: ["C"]},
        F: {x: null, y: null, id: 'F', edges: ["C"]},
        H: {x: null, y: null, id: 'H', edges: ["C"]},
        Z: {x: null, y: null, id: 'Z', edges: ["B"]},
        R: {x: null, y: null, id: 'R', edges: ["C"]},
        N: {x: null, y: null, id: 'N', edges: ["C"]},
        M: {x: null, y: null, id: 'M', edges: ["C"]},
        C: {x: null, y: null, id: 'C', edges: ["D"]},
        D: {x: null, y: null, id: 'D', edges: ["A"]}}).convertToArrays(),
      this.simulation = d3.forceSimulation(this.graph.nodes)
         //eslint:disable-next-line
           .force('charge', d3.forceManyBody())
           .force('link', d3.forceLink(this.graph.links).id(d => d.id))
           .force("center", d3.forceCenter(this.width / 2, this.height / 2));
      this.simulation.alpha(1)
      this.simulation.restart()
    },
    drop(){
      delete this.currentMove.node.fx
      delete this.currentMove.node.fy
      this.currentMove = null
      this.simulation.alpha(1)
      this.simulation.restart()
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
