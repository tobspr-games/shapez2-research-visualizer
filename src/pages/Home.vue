<template lang="pug">
.levels
    .level(v-for="node in Nodes()" :style="NodeStyle(node)", :data-category="node.CategoryId", :data-milestone="node.LevelIndex >= 0")
        .id {{ node.Id }}
        .title
            .milestoneIndex(v-if="node.LevelIndex >= 0") M{{ node.LevelIndex }}
            span(v-html="node.Title")
        .category(v-if="node.CategoryId") {{ node.CategoryId }}
        .desc {{ node.Description }}
        .goal {{ node.GoalAmount }} x {{ node.GoalShape }}
        .unlock(v-for="unlock in node.Unlocks") {{ unlock }}
        template(v-if="node.Dependencies")
            .dependency(v-for="dep in node.Dependencies") Requires {{ dep }}
        .dependency Requires Milestone {{ node.DependentLevelIndex }}
</template>

<script setup lang="ts">
import { reactive, ref } from "vue";
// import type { SerializedBeltLane } from "@/simulation/BeltLane";

import research from "@/data/research-metadata.json";

let categoryUpgradePositions: any = {};
let categoryQoLPositions: any = {};

function Nodes() {
    let result = [];
    for (let category of research.Categories) {
        for (let node of category.Nodes) {
            result.push(node);
        }
        for (let node of category.Upgrades) {
            result.push(node);
        }
    }
    for (let node of research.Levels) {
        result.push(node);
    }
    return result;
}

function NodePos(node: any) {
    if (node.LevelIndex >= 0) {
        return { left: 100 + node.LevelIndex * 250 + "px", top: "1000px" };
    }

    if (node.CategoryId == null) {
        throw new Error("Node without category: " + JSON.stringify(node));
    }

    var y = 0;
    if (node.Title.includes("<b>")) {
        // upgrade (@hack)
        if (typeof categoryUpgradePositions[node.CategoryId] === "undefined") {
            categoryUpgradePositions[node.CategoryId] = Object.keys(categoryUpgradePositions).length;
        }
        y = 2 + categoryUpgradePositions[node.CategoryId];
    } else {
        // qol
        if (typeof categoryQoLPositions[node.CategoryId] === "undefined") {
            categoryQoLPositions[node.CategoryId] = Object.keys(categoryQoLPositions).length;
        }
        y = -1 - categoryQoLPositions[node.CategoryId];
    }

    return { left: 250 + node.DependentLevelIndex * 250 + "px", top: 1000 + y * 200 + "px" };
}

function NodeStyle(node: any) {
    return { ...NodePos(node) };
}
</script>

<style lang="scss" scoped></style>
