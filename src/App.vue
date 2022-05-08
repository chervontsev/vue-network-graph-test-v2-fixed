<template>
  <div class="graph">
    <v-network-graph
      :nodes="nodes"
      :edges="edges"
      :configs="configs"
      :event-handlers="eventHandlers"
    >
      <template #override-node="{ nodeId, scale, config, ...slotProps }">
        <circle
          v-bind="slotProps"
          :ref="nodeId"
          :r="config.radius * scale"
          fill="white"
          stroke="lightgrey"
          :stroke-width="size.default"
        />
      </template>
    </v-network-graph>
  </div>
</template>

<script>
import * as vNG from "v-network-graph";
import { ForceLayout } from "v-network-graph/lib/force-layout";
import { nodes, edges, size, hues } from "./data";

export default {
  data: () => ({
    nodes,
    edges,
    hues,
    size,
    hoverIds: [],
  }),

  computed: {
    configs() {
      return vNG.defineConfigs({
        view: {
          layoutHandler: new ForceLayout(),
        },
        node: {
          label: { visible: true },
        },
        edge: {
          normal: {
            width: (edge) => {
              const { source, target } = edge;
              const { hoverIds } = this;
              return target === hoverIds[0] || source === hoverIds[0]
                ? this.size.hover
                : this.size.default;
            },
            color: (edge) => `hsla(${edge.hue}, 50%, 50%`,
          },
          hover: {
            width: this.size.hover,
            color: (edge) => `hsl(${edge.hue}, 50%, 50%`,
          },
        },
      });
    },
    eventHandlers() {
      return {
        "node:pointerover": ({ node }) => {
          this.handleHoverNode("in", node, "hover");
        },

        "node:pointerout": ({ node }) => {
          this.handleHoverNode("out", node, "default", "lightgrey");
        },

        "edge:pointerover": ({ edge }) => {
          this.handleHoverEdge(edge, "hover");
        },

        "edge:pointerout": ({ edge }) => {
          this.handleHoverEdge(edge, "default", "lightgrey");
        },
      };
    },
  },

  methods: {
    handleHoverNode(eventType, node, size, color) {
      this.edges.forEach((edge) => {
        const { source, target } = edge;
        if (target === node || source === node) {
          eventType === "in" && (this.hoverIds = [node, edge.target]);
          eventType === "out" && (this.hoverIds = []);
          const defaultColor = `hsl(${edge.hue}, 50%, 50%)`;
          this.$refs[edge.source].style.stroke = color ?? defaultColor;
          this.$refs[edge.source].style.strokeWidth = this.size[size];
          this.$refs[edge.target].style.stroke = color ?? defaultColor;
          this.$refs[edge.target].style.strokeWidth = this.size[size];
        }
      });
    },

    handleHoverEdge(edge, size, color) {
      const { source, target, hue } = this.edges[edge];
      const defaultColor = `hsl(${hue}, 50%, 50%)`;
      this.$refs[source].style.strokeWidth = this.size[size];
      this.$refs[target].style.strokeWidth = this.size[size];
      this.$refs[source].style.stroke = color ?? defaultColor;
      this.$refs[target].style.stroke = color ?? defaultColor;
    },
  },
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  font-family: sans-serif;
}

.graph,
.v-network-graph > svg {
  width: 100vw !important;
  height: 100vh !important;
}

.switch {
  position: fixed;
  top: 0;
  left: 0;
  display: flex;
  align-items: center;
  padding: 20px;
  background-color: #eee;
  cursor: pointer;
}

.switch input {
  width: 20px;
  height: 20px;
  margin: 0 10px 0 0;
  padding: 0;
}
</style>
