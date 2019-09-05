<template>
  <div class="mod-home">
    <h3>5G基站后台管理系统简介</h3>
    <p>
      &#8195;&#8195; 2019年是5G的商用元年，91个国家231家运营商挣积极进行5G试验及应用示范。作为5G关键技术专利数量位居全球首位的中国，其5G的发展更是不容小觑，这也使得5G基站后台的管理迫在眉睫。
    </p>
    <p>
      &#8195;&#8195; 本系统利用了SpringBoot和Vue实现了对5G基站后台设备的在线统计管理，系统具备地区管理、分类管理、设备管理、统计和预警管理五大功能，通过设备管理，将所有5G基站进行唯一编码，有效的满足了对基站的在线统计查看，出现异常后报警更换，及对基站维护频率生成报表在线查看。
    </p>
    <h3>主要技术</h3>
    <ul>前端
      <li>技术栈：Vue2.0 、element-ui、Bootstrap、Jquary</li>
      <li>网络请求：axios</li>
      <li>Router：动态添加</li>
      <li>报表统计：echarts</li>
      <li>地图展示：高德地图(Vue-aMap)，支持定位、搜索、展示搜索文本卡窗</li>
      <li>其他：本地测试通过mock配置对接口数据／mock模拟数据进行切换</li>
    </ul>
    <ul>后台
      <li>技术栈：  SpringBoot、MyBatisPlus</li>
      <li>权限控制： Shiro</li>
      <li>跨域： 基于Token验证和Cors配置</li>
    </ul>
    <ul>部署
      <li>Nginx：      待定</li>
      <li>Redis：     待定</li>
    </ul>
  </div>
</template>

<script>
  export default {
  }
</script>

<style>
  .mod-home {
    line-height: 1.5;
  }
</style>

