<template>
  <UploadExcel :on-success="success"></UploadExcel>
</template>

<script>
import EmployeeEnum from "@/constant/employees";
import { importEmployee } from "@/api/employees";
export default {
  methods: {
    async success({ header, results }) {
      const userRelations = {
        入职日期: "timeOfEntry",
        手机号: "mobile",
        姓名: "username",
        转正日期: "correctionTime",
        工号: "workNumber",
      };
      const array = results.map((item) => {
        const userInfo = {};
        Object.keys(item).forEach((key) => {
          if (
            userRelations[key] === "timeOfEntry" ||
            userRelations[key] === "correctionTime"
          ) {
            // 后端接口 限制了不能是字符串 要求转化时间类型
            userInfo[userRelations[key]] = new Date(
              this.formatDate(item[key], "/") // 只有这样 才能存入数据库
            );
          } else {
            userInfo[userRelations[key]] = item[key]; // 将原来中文对应的值  赋值给原来英文对应的值
          }
        });
        return userInfo;
      });
      await importEmployee(array);
      this.$message.success("导入excel成功");
      this.$router.back(); // 回到上一个页面
    },
    formatDate(numb, format) {
      const time = new Date((numb - 1) * 24 * 3600000 + 1);
      console.log(time);
      time.setYear(time.getFullYear() - 70);
      const year = time.getFullYear() + "";
      const month = time.getMonth() + 1 + "";
      const date = time.getDate() - 1 + "";
      if (format && format.length === 1) {
        return year + format + month + format + date;
      }
      return (
        year +
        (month < 10 ? "0" + month : month) +
        (date < 10 ? "0" + date : date)
      );
    },
  },
};
</script>

<style>
</style>
