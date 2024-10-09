<script setup>
import { onMounted } from 'vue';
const handleTextHighlight = (textContainer, textSearch) => {
  // 创建 createTreeWalker 迭代器，用于遍历文本节点，保存到一个数组
  const treeWalker = document.createTreeWalker( textContainer, NodeFilter.SHOW_TEXT);
  const allTextNodes = [];
  let currentNode = treeWalker.nextNode();
  while (currentNode) {
    allTextNodes.push(currentNode);
    currentNode = treeWalker.nextNode();
  }
  if (!CSS.highlights) {
    console.log("CSS Custom Highlight API not supported.")
    return;
  }
  CSS.highlights.clear();
  const ranges = allTextNodes
    .map((el) => {
      return { el, text: el.textContent.toLowerCase() };
    })
    .map(({ text, el }) => {
      const indices = [];
      let startPos = 0;
      while (startPos < text.length) {
        const index = text.indexOf(textSearch, startPos);
        if (index === -1) break;
        indices.push(index);
        startPos = index + textSearch.length;
      }

      // 根据搜索词的位置创建选区
      return indices.map((index) => {
        const range = new Range();
        range.setStart(el, index);
        range.setEnd(el, index + textSearch.length);
        return range;
      });
    });
  console.log('高亮文本范围', ranges);
  // 创建高亮对象
  const searchResultsHighlight = new Highlight(...ranges.flat());
  // 注册高亮, 对应::highlight(search-results) 样式
  CSS.highlights.set("search-results", searchResultsHighlight);
}

onMounted(()=> {
  let ele = document.getElementById('myContainer')
  handleTextHighlight( ele, '药')
})

</script>

<template>
  <div id="myContainer">
    不慎、无意或在无监督下的他克莫司胶囊和他克莫司缓释胶囊之间的转换是
    不安全的。这可能导致移植物排斥或增加不良反应发生，包括由于他克莫司全身
    暴露的临床相关差异而导致的免疫抑制不足或过度。患者应维持他克莫司单一剂
    型及相应的日给药方案进行治疗。改变剂型或调整剂量只能在移植专家严密的监
    督下进行。任何剂型转换后，都需要监测治疗药物，并调整剂量以保证他克莫司
  </div>
</template>

<style scoped lang="scss">
#myContainer{
  &::highlight(search-results) {
    background-color: #5949E3;
    color: white;
  }
}
</style>
