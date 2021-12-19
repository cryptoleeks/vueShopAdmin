<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input
            placeholder="请输入内容"
            clearable
            v-model="queryInfo.keyword"
            @clear="getGoodsList"
          >
            <el-button
              slot="append"
              icon="el-icon-search"
              @click="handleCurrentChange(1);getGoodsList;"
            ></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="goAddPage">添加商品</el-button>
        </el-col>
      </el-row>

      <el-table :data="goodslist" stripe border style="width: 100%">
        <el-table-column type="index"> </el-table-column>
        <el-table-column prop="title" label="商品名称"></el-table-column>
        <el-table-column
          prop="market_price"
          label="商品原价（元）"
          width="95px"
        ></el-table-column>
        <el-table-column
          prop="sell_price"
          label="商品售价"
          width="70px"
        ></el-table-column>
        <el-table-column prop="add_time" label="创建时间" width="170px">
          <template v-slot="scope">
            {{ scope.row.add_time | dateFormat }}
          </template>
        </el-table-column>
        <el-table-column label="操作" width="130px">
          <template v-slot="scope">
            <el-button
              size="mini"
              type="primary"
              icon="el-icon-edit"
            ></el-button>
            <el-button
              size="mini"
              type="warning"
              icon="el-icon-delete"
              @click="removeById(scope.row.id)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>

      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pageIndex"
        :page-sizes="[10, 20, 50]"
        :page-size="queryInfo.pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
        background
      >
      </el-pagination>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      queryInfo: {
        keyword: "",
        pageIndex: 1,
        pageSize: 10
      },
      goodslist: [],
      total: 0
    };
  },
  methods: {
    async getGoodsList() {
      const { data } = await this.$http.get("goods/list", {
        params: this.queryInfo
      });

      if (data.status !== 0) {
        return this.$message.error(data.meta.msg);
      }
      console.log(data)
      this.goodslist = data.message;
      this.total = data.total;
    },
    handleSizeChange(newSize) {
      this.queryInfo.pageSize = newSize;
      this.getGoodsList();
    },
    handleCurrentChange(newPage) {
      this.queryInfo.pageIndex = newPage;
      this.getGoodsList();
    },
    removeById(id) {
      this.$confirm("此操作将永久删除该商品, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const { data } = await this.$http.delete(`goods/${id}`);
          if (data.status !== 0) {
            return this.$message.error(data.message);
          }
          this.getGoodsList();
          this.$message({
            type: "success",
            message: "删除成功!"
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    goAddPage() {
      this.$router.push("goods/add");
    }
  },
  created() {
    this.getGoodsList();
  }
};
</script>

<style lang="less" scoped></style>
