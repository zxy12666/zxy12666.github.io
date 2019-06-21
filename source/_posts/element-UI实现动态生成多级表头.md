title: element UI实现动态生成多级表头
author: 张翔宇
tags:
  - vue
categories:
  - vue
date: 2019-05-05 09:15:00
---
<div class="post">

**一、效果图**

![Markdown](http://i1.fuimg.com/691375/95ac80c22df10c32.png)

![Markdown](http://i1.fuimg.com/691375/0700d9700a435779s.png)

**二、封装两个组件，分别为DynamicTable.vue和TableColumn.vue，TableColumn.vue主要是使用递归来对表头进行循环生成**

DynamicTable.vue


<pre><span style="color: #008080;"> 1</span> &lt;template&gt;
<span style="color: #008080;"> 2</span>   &lt;el-table :data="tableData" border :height="height"&gt;
<span style="color: #008080;"> 3</span>     &lt;template v-<span style="color: #0000ff;">for</span>="item in tableHeader"&gt;
<span style="color: #008080;"> 4</span>       &lt;table-column v-<span style="color: #0000ff;">if</span>="item.children &amp;&amp; item.children.length" :key="item.id" :coloumn-header="item"&gt;&lt;/table-column&gt;
<span style="color: #008080;"> 5</span>       &lt;el-table-column v-<span style="color: #0000ff;">else</span> :key="item.id" :label="item.label" :prop="item.prop" align="center"&gt;&lt;/el-table-column&gt;
<span style="color: #008080;"> 6</span>     &lt;/template&gt;
<span style="color: #008080;"> 7</span>   &lt;/el-table&gt;
<span style="color: #008080;"> 8</span> &lt;/template&gt;
<span style="color: #008080;"> 9</span> 
<span style="color: #008080;">10</span> &lt;script&gt;
<span style="color: #008080;">11</span> import TableColumn from './TableColumn'
<span style="color: #008080;">12</span> export <span style="color: #0000ff;">default</span><span style="color: #000000;"> {
</span><span style="color: #008080;">13</span> <span style="color: #000000;">  props: {
</span><span style="color: #008080;">14</span>     <span style="color: #008000;">//</span><span style="color: #008000;"> 表格的数据</span>
<span style="color: #008080;">15</span> <span style="color: #000000;">    tableData: {
</span><span style="color: #008080;">16</span> <span style="color: #000000;">      type: Array,
</span><span style="color: #008080;">17</span>       required: <span style="color: #0000ff;">true</span>
<span style="color: #008080;">18</span> <span style="color: #000000;">    },
</span><span style="color: #008080;">19</span>     <span style="color: #008000;">//</span><span style="color: #008000;"> 多级表头的数据</span>
<span style="color: #008080;">20</span> <span style="color: #000000;">    tableHeader: {
</span><span style="color: #008080;">21</span> <span style="color: #000000;">      type: Array,
</span><span style="color: #008080;">22</span>       required: <span style="color: #0000ff;">true</span>
<span style="color: #008080;">23</span> <span style="color: #000000;">    },
</span><span style="color: #008080;">24</span>     <span style="color: #008000;">//</span><span style="color: #008000;"> 表格的高度</span>
<span style="color: #008080;">25</span> <span style="color: #000000;">    height: {
</span><span style="color: #008080;">26</span> <span style="color: #000000;">      type: String,
</span><span style="color: #008080;">27</span>       <span style="color: #0000ff;">default</span>: '300'
<span style="color: #008080;">28</span> <span style="color: #000000;">    }
</span><span style="color: #008080;">29</span> <span style="color: #000000;">  },
</span><span style="color: #008080;">30</span> <span style="color: #000000;">  components: {
</span><span style="color: #008080;">31</span> <span style="color: #000000;">    TableColumn
</span><span style="color: #008080;">32</span> <span style="color: #000000;">  }
</span><span style="color: #008080;">33</span> <span style="color: #000000;">}
</span><span style="color: #008080;">34</span> &lt;/script&gt;
<span style="color: #008080;">35</span> 
<span style="color: #008080;">36</span> &lt;style scoped&gt;
<span style="color: #008080;">37</span> 
<span style="color: #008080;">38</span> &lt;/style&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy">[![复制代码](//common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")</span></div></div>

TableColumn.vue


<pre>&lt;template&gt;
  &lt;el-table-column :label="coloumnHeader.label" :prop="coloumnHeader.label" align="center"&gt;
    &lt;template v-<span style="color: #0000ff;">for</span>="item in coloumnHeader.children"&gt;
      &lt;tableColumn v-<span style="color: #0000ff;">if</span>="item.children &amp;&amp; item.children.length" :key="item.id" :coloumn-header="item"&gt;&lt;/tableColumn&gt;
      &lt;el-table-column v-<span style="color: #0000ff;">else</span> :key="item.name" :label="item.label" :prop="item.prop" align="center"&gt;&lt;/el-table-column&gt;
    &lt;/template&gt;
  &lt;/el-table-column&gt;
&lt;/template&gt;

&lt;script&gt;<span style="color: #000000;">
export </span><span style="color: #0000ff;">default</span><span style="color: #000000;"> {
  name: </span>'tableColumn'<span style="color: #000000;">,
  props: {
    coloumnHeader: {
      type: Object,
      required: </span><span style="color: #0000ff;">true</span><span style="color: #000000;">
    }
  }
}
</span>&lt;/script&gt;

&lt;style scoped&gt;

&lt;/style&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy">[![复制代码](//common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")</span></div></div>

**三、使用**

HTML代码

<div class="cnblogs_code">
<pre><span style="color: #008080;">1</span>     &lt;div class="result-wrapper"&gt;
<span style="color: #008080;">2</span>       &lt;dynamic-table :table-data="tableData" :table-header="tableConfig" v-<span style="color: #0000ff;">if</span>="dynamicTableShow"&gt;&lt;/dynamic-table&gt;
<span style="color: #008080;">3</span>       &lt;dynamic-form v-<span style="color: #0000ff;">else</span>&gt;&lt;/dynamic-form&gt;
<span style="color: #008080;">4</span>     &lt;/div&gt;</pre>
</div>

JS代码


<pre><span style="color: #008080;">  1</span> &lt;script&gt;
<span style="color: #008080;">  2</span> import DynamicTable from './components/DynamicTable'
<span style="color: #008080;">  3</span> export <span style="color: #0000ff;">default</span><span style="color: #000000;"> {
</span><span style="color: #008080;">  4</span> <span style="color: #000000;">  components: {
</span><span style="color: #008080;">  5</span> <span style="color: #000000;">    DynamicTable
</span><span style="color: #008080;">  6</span> <span style="color: #000000;">  },
</span><span style="color: #008080;">  7</span> <span style="color: #000000;">  data () {
</span><span style="color: #008080;">  8</span>     <span style="color: #0000ff;">return</span><span style="color: #000000;"> {
</span><span style="color: #008080;">  9</span> <span style="color: #000000;">      searchForm: {
</span><span style="color: #008080;"> 10</span> <span style="color: #000000;">        month: getMonth(),
</span><span style="color: #008080;"> 11</span>         serviceCategory: '1'
<span style="color: #008080;"> 12</span> <span style="color: #000000;">      },
</span><span style="color: #008080;"> 13</span>       dynamicTableShow: <span style="color: #0000ff;">true</span>, <span style="color: #008000;">//</span><span style="color: #008000;"> 使得DynamicTable组件重新渲染变量</span>
<span style="color: #008080;"> 14</span>       <span style="color: #008000;">//</span><span style="color: #008000;"> 表数据</span>
<span style="color: #008080;"> 15</span> <span style="color: #000000;">      tableData: [
</span><span style="color: #008080;"> 16</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 17</span>           districtName: '1'<span style="color: #000000;">,
</span><span style="color: #008080;"> 18</span>           timeDimension: '2'<span style="color: #000000;">,
</span><span style="color: #008080;"> 19</span>           residentPopNum: '3'<span style="color: #000000;">,
</span><span style="color: #008080;"> 20</span>           residentPopDst: '4'<span style="color: #000000;">,
</span><span style="color: #008080;"> 21</span>           liveLandArea: '5'<span style="color: #000000;">,
</span><span style="color: #008080;"> 22</span>           liveLandDst: '6'<span style="color: #000000;">,
</span><span style="color: #008080;"> 23</span>           employmentLandArea: '7'<span style="color: #000000;">,
</span><span style="color: #008080;"> 24</span>           employmentLandDst: '8'
<span style="color: #008080;"> 25</span> <span style="color: #000000;">        }
</span><span style="color: #008080;"> 26</span> <span style="color: #000000;">      ],
</span><span style="color: #008080;"> 27</span>       <span style="color: #008000;">//</span><span style="color: #008000;"> 表头数据</span>
<span style="color: #008080;"> 28</span> <span style="color: #000000;">      tableConfig: [
</span><span style="color: #008080;"> 29</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 30</span>           id: 100<span style="color: #000000;">,
</span><span style="color: #008080;"> 31</span>           label: '一级表头'<span style="color: #000000;">,
</span><span style="color: #008080;"> 32</span>           prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;"> 33</span> <span style="color: #000000;">          children: [
</span><span style="color: #008080;"> 34</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 35</span>               id: 110<span style="color: #000000;">,
</span><span style="color: #008080;"> 36</span>               label: '二级表头1'<span style="color: #000000;">,
</span><span style="color: #008080;"> 37</span>               prop: 'districtName'
<span style="color: #008080;"> 38</span> <span style="color: #000000;">            },
</span><span style="color: #008080;"> 39</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 40</span>               id: 120<span style="color: #000000;">,
</span><span style="color: #008080;"> 41</span>               label: '二级表头2'<span style="color: #000000;">,
</span><span style="color: #008080;"> 42</span>               prop: 'timeDimension'
<span style="color: #008080;"> 43</span> <span style="color: #000000;">            }
</span><span style="color: #008080;"> 44</span> <span style="color: #000000;">          ]
</span><span style="color: #008080;"> 45</span> <span style="color: #000000;">        },
</span><span style="color: #008080;"> 46</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 47</span>           id: 200<span style="color: #000000;">,
</span><span style="color: #008080;"> 48</span>           label: '一级表头1'<span style="color: #000000;">,
</span><span style="color: #008080;"> 49</span>           prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;"> 50</span> <span style="color: #000000;">          children: [
</span><span style="color: #008080;"> 51</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 52</span>               id: 210<span style="color: #000000;">,
</span><span style="color: #008080;"> 53</span>               label: '二级表头2'<span style="color: #000000;">,
</span><span style="color: #008080;"> 54</span>               prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;"> 55</span> <span style="color: #000000;">              children: [
</span><span style="color: #008080;"> 56</span> <span style="color: #000000;">                {
</span><span style="color: #008080;"> 57</span>                   id: 211<span style="color: #000000;">,
</span><span style="color: #008080;"> 58</span>                   label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;"> 59</span>                   prop: 'residentPopNum'
<span style="color: #008080;"> 60</span> <span style="color: #000000;">                },
</span><span style="color: #008080;"> 61</span> <span style="color: #000000;">                {
</span><span style="color: #008080;"> 62</span>                   id: 212<span style="color: #000000;">,
</span><span style="color: #008080;"> 63</span>                   label: '三级表头'<span style="color: #000000;">,
</span><span style="color: #008080;"> 64</span>                   prop: 'residentPopDst'
<span style="color: #008080;"> 65</span> <span style="color: #000000;">                }
</span><span style="color: #008080;"> 66</span> <span style="color: #000000;">              ]
</span><span style="color: #008080;"> 67</span> <span style="color: #000000;">            }
</span><span style="color: #008080;"> 68</span> <span style="color: #000000;">          ]
</span><span style="color: #008080;"> 69</span> <span style="color: #000000;">        },
</span><span style="color: #008080;"> 70</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 71</span>           id: 300<span style="color: #000000;">,
</span><span style="color: #008080;"> 72</span>           label: '一级表头1'<span style="color: #000000;">,
</span><span style="color: #008080;"> 73</span>           prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;"> 74</span> <span style="color: #000000;">          children: [
</span><span style="color: #008080;"> 75</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 76</span>               id: 310<span style="color: #000000;">,
</span><span style="color: #008080;"> 77</span>               label: '二级表头2'<span style="color: #000000;">,
</span><span style="color: #008080;"> 78</span>               prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;"> 79</span> <span style="color: #000000;">              children: [
</span><span style="color: #008080;"> 80</span> <span style="color: #000000;">                {
</span><span style="color: #008080;"> 81</span>                   id: 311<span style="color: #000000;">,
</span><span style="color: #008080;"> 82</span>                   label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;"> 83</span>                   prop: 'liveLandArea'
<span style="color: #008080;"> 84</span> <span style="color: #000000;">                },
</span><span style="color: #008080;"> 85</span> <span style="color: #000000;">                {
</span><span style="color: #008080;"> 86</span>                   id: 312<span style="color: #000000;">,
</span><span style="color: #008080;"> 87</span>                   label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;"> 88</span>                   prop: 'liveLandDst'
<span style="color: #008080;"> 89</span> <span style="color: #000000;">                }
</span><span style="color: #008080;"> 90</span> <span style="color: #000000;">              ]
</span><span style="color: #008080;"> 91</span> <span style="color: #000000;">            },
</span><span style="color: #008080;"> 92</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 93</span>               id: 320<span style="color: #000000;">,
</span><span style="color: #008080;"> 94</span>               label: '二级表头1'<span style="color: #000000;">,
</span><span style="color: #008080;"> 95</span>               prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;"> 96</span> <span style="color: #000000;">              children: [
</span><span style="color: #008080;"> 97</span> <span style="color: #000000;">                {
</span><span style="color: #008080;"> 98</span>                   id: 321<span style="color: #000000;">,
</span><span style="color: #008080;"> 99</span>                   label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;">100</span>                   prop: 'employmentLandArea'
<span style="color: #008080;">101</span> <span style="color: #000000;">                },
</span><span style="color: #008080;">102</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">103</span>                   id: 322<span style="color: #000000;">,
</span><span style="color: #008080;">104</span>                   label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;">105</span>                   prop: 'employmentLandDst'
<span style="color: #008080;">106</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">107</span> <span style="color: #000000;">              ]
</span><span style="color: #008080;">108</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">109</span> <span style="color: #000000;">          ]
</span><span style="color: #008080;">110</span> <span style="color: #000000;">        }
</span><span style="color: #008080;">111</span> <span style="color: #000000;">      ]
</span><span style="color: #008080;">112</span> <span style="color: #000000;">    }
</span><span style="color: #008080;">113</span> <span style="color: #000000;">  },
</span><span style="color: #008080;">114</span> <span style="color: #000000;">  methods: {
</span><span style="color: #008080;">115</span>     <span style="color: #008000;">//</span><span style="color: #008000;"> 服务类型改变的时候，需要重新请求表头信息和表格数据</span>
<span style="color: #008080;">116</span> <span style="color: #000000;">    handleServiceCategoryChange (val) {
</span><span style="color: #008080;">117</span>       <span style="color: #008000;">//</span><span style="color: #008000;"> 设置dynamicTableShow为false，使得DynamicTable组件重新渲染</span>
<span style="color: #008080;">118</span>       <span style="color: #0000ff;">this</span>.dynamicTableShow = <span style="color: #0000ff;">false</span>
<span style="color: #008080;">119</span>       <span style="color: #0000ff;">if</span> (val === '1'<span style="color: #000000;">) {
</span><span style="color: #008080;">120</span>         <span style="color: #0000ff;">this</span>.tableData =<span style="color: #000000;"> [
</span><span style="color: #008080;">121</span> <span style="color: #000000;">          {
</span><span style="color: #008080;">122</span>             districtName: '1'<span style="color: #000000;">,
</span><span style="color: #008080;">123</span>             timeDimension: '2'<span style="color: #000000;">,
</span><span style="color: #008080;">124</span>             residentPopNum: '3'<span style="color: #000000;">,
</span><span style="color: #008080;">125</span>             residentPopDst: '4'<span style="color: #000000;">,
</span><span style="color: #008080;">126</span>             liveLandArea: '5'<span style="color: #000000;">,
</span><span style="color: #008080;">127</span>             liveLandDst: '6'<span style="color: #000000;">,
</span><span style="color: #008080;">128</span>             employmentLandArea: '7'<span style="color: #000000;">,
</span><span style="color: #008080;">129</span>             employmentLandDst: '8'
<span style="color: #008080;">130</span> <span style="color: #000000;">          }
</span><span style="color: #008080;">131</span> <span style="color: #000000;">        ]
</span><span style="color: #008080;">132</span>         <span style="color: #0000ff;">this</span>.tableConfig =<span style="color: #000000;"> [
</span><span style="color: #008080;">133</span> <span style="color: #000000;">          {
</span><span style="color: #008080;">134</span>             id: 100<span style="color: #000000;">,
</span><span style="color: #008080;">135</span>             label: '一级表头'<span style="color: #000000;">,
</span><span style="color: #008080;">136</span>             prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;">137</span> <span style="color: #000000;">            children: [
</span><span style="color: #008080;">138</span> <span style="color: #000000;">              {
</span><span style="color: #008080;">139</span>                 id: 110<span style="color: #000000;">,
</span><span style="color: #008080;">140</span>                 label: '二级表头1'<span style="color: #000000;">,
</span><span style="color: #008080;">141</span>                 prop: 'districtName'
<span style="color: #008080;">142</span> <span style="color: #000000;">              },
</span><span style="color: #008080;">143</span> <span style="color: #000000;">              {
</span><span style="color: #008080;">144</span>                 id: 120<span style="color: #000000;">,
</span><span style="color: #008080;">145</span>                 label: '二级表头2'<span style="color: #000000;">,
</span><span style="color: #008080;">146</span>                 prop: 'timeDimension'
<span style="color: #008080;">147</span> <span style="color: #000000;">              }
</span><span style="color: #008080;">148</span> <span style="color: #000000;">            ]
</span><span style="color: #008080;">149</span> <span style="color: #000000;">          },
</span><span style="color: #008080;">150</span> <span style="color: #000000;">          {
</span><span style="color: #008080;">151</span>             id: 200<span style="color: #000000;">,
</span><span style="color: #008080;">152</span>             label: '一级表头1'<span style="color: #000000;">,
</span><span style="color: #008080;">153</span>             prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;">154</span> <span style="color: #000000;">            children: [
</span><span style="color: #008080;">155</span> <span style="color: #000000;">              {
</span><span style="color: #008080;">156</span>                 id: 210<span style="color: #000000;">,
</span><span style="color: #008080;">157</span>                 label: '二级表头2'<span style="color: #000000;">,
</span><span style="color: #008080;">158</span>                 prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;">159</span> <span style="color: #000000;">                children: [
</span><span style="color: #008080;">160</span> <span style="color: #000000;">                  {
</span><span style="color: #008080;">161</span>                     id: 211<span style="color: #000000;">,
</span><span style="color: #008080;">162</span>                     label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;">163</span>                     prop: 'residentPopNum'
<span style="color: #008080;">164</span> <span style="color: #000000;">                  },
</span><span style="color: #008080;">165</span> <span style="color: #000000;">                  {
</span><span style="color: #008080;">166</span>                     id: 212<span style="color: #000000;">,
</span><span style="color: #008080;">167</span>                     label: '三级表头'<span style="color: #000000;">,
</span><span style="color: #008080;">168</span>                     prop: 'residentPopDst'
<span style="color: #008080;">169</span> <span style="color: #000000;">                  }
</span><span style="color: #008080;">170</span> <span style="color: #000000;">                ]
</span><span style="color: #008080;">171</span> <span style="color: #000000;">              }
</span><span style="color: #008080;">172</span> <span style="color: #000000;">            ]
</span><span style="color: #008080;">173</span> <span style="color: #000000;">          },
</span><span style="color: #008080;">174</span> <span style="color: #000000;">          {
</span><span style="color: #008080;">175</span>             id: 300<span style="color: #000000;">,
</span><span style="color: #008080;">176</span>             label: '一级表头1'<span style="color: #000000;">,
</span><span style="color: #008080;">177</span>             prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;">178</span> <span style="color: #000000;">            children: [
</span><span style="color: #008080;">179</span> <span style="color: #000000;">              {
</span><span style="color: #008080;">180</span>                 id: 310<span style="color: #000000;">,
</span><span style="color: #008080;">181</span>                 label: '二级表头2'<span style="color: #000000;">,
</span><span style="color: #008080;">182</span>                 prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;">183</span> <span style="color: #000000;">                children: [
</span><span style="color: #008080;">184</span> <span style="color: #000000;">                  {
</span><span style="color: #008080;">185</span>                     id: 311<span style="color: #000000;">,
</span><span style="color: #008080;">186</span>                     label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;">187</span>                     prop: 'liveLandArea'
<span style="color: #008080;">188</span> <span style="color: #000000;">                  },
</span><span style="color: #008080;">189</span> <span style="color: #000000;">                  {
</span><span style="color: #008080;">190</span>                     id: 312<span style="color: #000000;">,
</span><span style="color: #008080;">191</span>                     label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;">192</span>                     prop: 'liveLandDst'
<span style="color: #008080;">193</span> <span style="color: #000000;">                  }
</span><span style="color: #008080;">194</span> <span style="color: #000000;">                ]
</span><span style="color: #008080;">195</span> <span style="color: #000000;">              },
</span><span style="color: #008080;">196</span> <span style="color: #000000;">              {
</span><span style="color: #008080;">197</span>                 id: 320<span style="color: #000000;">,
</span><span style="color: #008080;">198</span>                 label: '二级表头1'<span style="color: #000000;">,
</span><span style="color: #008080;">199</span>                 prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;">200</span> <span style="color: #000000;">                children: [
</span><span style="color: #008080;">201</span> <span style="color: #000000;">                  {
</span><span style="color: #008080;">202</span>                     id: 321<span style="color: #000000;">,
</span><span style="color: #008080;">203</span>                     label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;">204</span>                     prop: 'employmentLandArea'
<span style="color: #008080;">205</span> <span style="color: #000000;">                  },
</span><span style="color: #008080;">206</span> <span style="color: #000000;">                  {
</span><span style="color: #008080;">207</span>                     id: 322<span style="color: #000000;">,
</span><span style="color: #008080;">208</span>                     label: '三级表头3'<span style="color: #000000;">,
</span><span style="color: #008080;">209</span>                     prop: 'employmentLandDst'
<span style="color: #008080;">210</span> <span style="color: #000000;">                  }
</span><span style="color: #008080;">211</span> <span style="color: #000000;">                ]
</span><span style="color: #008080;">212</span> <span style="color: #000000;">              }
</span><span style="color: #008080;">213</span> <span style="color: #000000;">            ]
</span><span style="color: #008080;">214</span> <span style="color: #000000;">          }
</span><span style="color: #008080;">215</span> <span style="color: #000000;">        ]
</span><span style="color: #008080;">216</span>       } <span style="color: #0000ff;">else</span><span style="color: #000000;"> {
</span><span style="color: #008080;">217</span>         <span style="color: #0000ff;">this</span>.tableData =<span style="color: #000000;"> [
</span><span style="color: #008080;">218</span> <span style="color: #000000;">          {
</span><span style="color: #008080;">219</span>             districtName: '111'<span style="color: #000000;">,
</span><span style="color: #008080;">220</span>             timeDimension: '222'
<span style="color: #008080;">221</span> <span style="color: #000000;">          }
</span><span style="color: #008080;">222</span> <span style="color: #000000;">        ]
</span><span style="color: #008080;">223</span>         <span style="color: #0000ff;">this</span>.tableConfig =<span style="color: #000000;"> [
</span><span style="color: #008080;">224</span> <span style="color: #000000;">          {
</span><span style="color: #008080;">225</span>             id: 100<span style="color: #000000;">,
</span><span style="color: #008080;">226</span>             label: '一级表头'<span style="color: #000000;">,
</span><span style="color: #008080;">227</span>             prop: ''<span style="color: #000000;">,
</span><span style="color: #008080;">228</span> <span style="color: #000000;">            children: [
</span><span style="color: #008080;">229</span> <span style="color: #000000;">              {
</span><span style="color: #008080;">230</span>                 id: 110<span style="color: #000000;">,
</span><span style="color: #008080;">231</span>                 label: '二级表头1'<span style="color: #000000;">,
</span><span style="color: #008080;">232</span>                 prop: 'districtName'
<span style="color: #008080;">233</span> <span style="color: #000000;">              },
</span><span style="color: #008080;">234</span> <span style="color: #000000;">              {
</span><span style="color: #008080;">235</span>                 id: 120<span style="color: #000000;">,
</span><span style="color: #008080;">236</span>                 label: '二级表头2'<span style="color: #000000;">,
</span><span style="color: #008080;">237</span>                 prop: 'timeDimension'
<span style="color: #008080;">238</span> <span style="color: #000000;">              }
</span><span style="color: #008080;">239</span> <span style="color: #000000;">            ]
</span><span style="color: #008080;">240</span> <span style="color: #000000;">          }
</span><span style="color: #008080;">241</span> <span style="color: #000000;">        ]
</span><span style="color: #008080;">242</span> <span style="color: #000000;">      }
</span><span style="color: #008080;">243</span>       <span style="color: #008000;">//</span><span style="color: #008000;"> 此处是DOM还没有更新，此处的代码是必须的</span>
<span style="color: #008080;">244</span>       <span style="color: #0000ff;">this</span>.$nextTick(() =&gt;<span style="color: #000000;"> {
</span><span style="color: #008080;">245</span>         <span style="color: #008000;">//</span><span style="color: #008000;"> DOM 现在更新了</span>
<span style="color: #008080;">246</span>         <span style="color: #0000ff;">this</span>.dynamicTableShow = <span style="color: #0000ff;">true</span>
<span style="color: #008080;">247</span> <span style="color: #000000;">      })
</span><span style="color: #008080;">248</span> <span style="color: #000000;">    }
</span><span style="color: #008080;">249</span> <span style="color: #000000;">  }
</span><span style="color: #008080;">250</span> <span style="color: #000000;">}
</span><span style="color: #008080;">251</span> &lt;/script&gt;
<span style="color: #008080;">252</span> 
<span style="color: #008080;">253</span> &lt;style scoped&gt;
<span style="color: #008080;">254</span> .policy-<span style="color: #000000;">wrapper{
</span><span style="color: #008080;">255</span>   margin-<span style="color: #000000;">top: 10px;
</span><span style="color: #008080;">256</span> <span style="color: #000000;">}
</span><span style="color: #008080;">257</span> .result-<span style="color: #000000;">wrapper{
</span><span style="color: #008080;">258</span>   margin-<span style="color: #000000;">top: 22px;
</span><span style="color: #008080;">259</span> <span style="color: #000000;">}
</span><span style="color: #008080;">260</span> &lt;/style&gt;</pre>