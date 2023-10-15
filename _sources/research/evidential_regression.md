# Evidential Learning and Uncentainty

```{note}
In this chapter we present uncentainty estimation and a specific method called evidential learning to compute uncentainty.

<script type="text/javascript">
function lastModified() {
    var modiDate = new Date(document.lastModified);
    var showAs = modiDate.getDate() + "-" + (modiDate.getMonth() + 1) + "-" + modiDate.getFullYear();
    return showAs
}
document.writeln("<div style='font-size: 14px;'>");
document.write("Last updated on " + lastModified());
document.writeln("</div>");
</script>
```
## The classification of uncentainty

> Predictive Uncertainty Estimation via Prior Networks, 2018

```{admonition} Abstract:
:class: important

Uncertainty可以通过**uncertainty in model parameters**，**irreducible data uncertainty**和**uncertainty due to distributional mismatch** between the test and training data distributions进行估计。本文提出了一个先验网络显示学习distributional uncertainty，通过参数化一个预测分布的先验分布实现。并且本文对uncentainty的分类比较清晰

```
{cite:p}`malinin2018predictive` 将uncentainty分为三类。
model uncertainty (epistemic uncertainty)
data uncertainty
distributional uncertainty




# Evidential Regression


```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/N0QLOZ.png
---
height: 150px
name: blackhole
---
Depiction of a black hole as reconstructed from data recorded by the Event Horizon Telescope.
```