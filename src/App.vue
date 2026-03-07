<template>
  <div class="wrap">
    <h2 class="title">24移动互联3-2班 李成龙 购物车</h2>

    <!-- 表头 -->
    <div class="table head">
      <div class="cell c-check">
        <label class="check">
          <input type="checkbox" :checked="isAllChecked" @change="toggleAll($event)" />
          <span>全选</span>
        </label>
      </div>
      <div class="cell c-name">商品名称</div>
      <div class="cell c-price">单价</div>
      <div class="cell c-qty">数量</div>
      <div class="cell c-sub">小计</div>
      <div class="cell c-op">操作</div>
    </div>

    <!-- 商品行 -->
    <div v-if="items.length === 0" class="empty">购物车为空</div>

    <div v-for="item in items" :key="item.id" class="table row">
      <div class="cell c-check">
        <input type="checkbox" v-model="item.checked" />
      </div>

      <div class="cell c-name">
        <div class="prod">
          <img class="img" :src="item.image" alt="" />
          <div class="name">{{ item.name }}</div>
        </div>
      </div>

      <div class="cell c-price">￥{{ item.price.toFixed(2) }}</div>

      <div class="cell c-qty">
        <div class="qty">
          <button class="qty-btn" :disabled="item.qty <= 1" @click="dec(item)">-</button>
          <input class="qty-input" :value="item.qty" @input="onQtyInput(item, $event)" />
          <button class="qty-btn" @click="inc(item)">+</button>
        </div>
      </div>

      <div class="cell c-sub">
        ￥{{ (item.price * item.qty).toFixed(2) }}
      </div>

      <div class="cell c-op">
        <button class="x" title="删除" @click="removeOne(item.id)">×</button>
      </div>
    </div>

    <!-- 底部结算条 -->
    <div class="bar">
      <div class="bar-left">
        <span>已选 {{ checkedCount }} 件商品</span>
        <button class="link" @click="removeChecked" :disabled="checkedCount === 0">删除所选商品</button>
        <button class="link" @click="clearAll" :disabled="items.length === 0">清空购物车</button>
      </div>

      <div class="bar-right">
        <div class="sum">
          合计：<span class="money">￥{{ checkedTotal.toFixed(2) }}</span>
        </div>
        <button class="checkout" :disabled="checkedCount === 0" @click="checkout">
          去结算
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, reactive } from "vue";

/** 演示商品（你可以换成你自己的商品/图片） */
const items = reactive([
  {
    id: 1,
    checked: true,
    name: "多彩粉尘积木",
    price: 9.9,
    qty: 1,
    image: "https://picsum.photos/seed/p1/80/80",
  },
  {
    id: 2,
    checked: false,
    name: "小米路由器4A 纯白色",
    price: 9.9,
    qty: 1,
    image: "https://picsum.photos/seed/p2/80/80",
  },
  {
    id: 3,
    checked: true,
    name: "小米10Pro 纯白色",
    price: 1999.0,
    qty: 1,
    image: "https://picsum.photos/seed/p3/80/80",
  },
]);

/** 是否全选 */
const isAllChecked = computed(() => items.length > 0 && items.every((i) => i.checked));

/** 已选件数（按数量累加） */
const checkedCount = computed(() => {
  let sum = 0;
  items.forEach((i) => {
    if (i.checked) sum += i.qty;
  });
  return sum;
});

/** 已选总价 */
const checkedTotal = computed(() => {
  let sum = 0;
  items.forEach((i) => {
    if (i.checked) sum += i.price * i.qty;
  });
  return sum;
});

function toggleAll(e) {
  const v = e.target.checked;
  items.forEach((i) => (i.checked = v));
}

function inc(item) {
  item.qty += 1;
}

function dec(item) {
  if (item.qty > 1) item.qty -= 1;
}

function onQtyInput(item, e) {
  const raw = e.target.value;
  const n = Number(String(raw).replace(/[^\d]/g, ""));
  item.qty = Number.isFinite(n) && n >= 1 ? n : 1;
}

function removeOne(id) {
  const idx = items.findIndex((i) => i.id === id);
  if (idx !== -1) items.splice(idx, 1);
}

function removeChecked() {
  for (let i = items.length - 1; i >= 0; i--) {
    if (items[i].checked) items.splice(i, 1);
  }
}

function clearAll() {
  items.splice(0, items.length);
}

function checkout() {
  alert(`去结算（演示）\n件数：${checkedCount.value}\n合计：￥${checkedTotal.value.toFixed(2)}`);
}
</script>

<style scoped>
.wrap {
  max-width: 980px;
  margin: 24px auto 110px;
  padding: 0 16px;
  font-family: system-ui, -apple-system, "Segoe UI", Roboto, Arial, "Noto Sans", sans-serif;
  color: #111827;
}

.title {
  margin: 0 0 14px;
  font-size: 20px;
  font-weight: 800;
}

.table {
  display: grid;
  grid-template-columns: 90px 1fr 120px 180px 120px 90px;
  align-items: center;
  border: 1px solid #e5e7eb;
  border-top: none;
  background: #fff;
}

.head {
  border-top: 1px solid #e5e7eb;
  background: #fafafa;
  font-weight: 700;
  color: #374151;
}

.row:hover {
  background: #fcfcfc;
}

.cell {
  padding: 14px 12px;
  border-right: 1px solid #f1f5f9;
}

.cell:last-child {
  border-right: none;
}

.c-check {
  display: flex;
  align-items: center;
  gap: 8px;
  justify-content: center;
}

.check {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  user-select: none;
}

.prod {
  display: flex;
  align-items: center;
  gap: 12px;
}

.img {
  width: 56px;
  height: 56px;
  border-radius: 10px;
  border: 1px solid #e5e7eb;
  object-fit: cover;
}

.name {
  font-weight: 600;
  color: #111827;
}

.c-price,
.c-sub {
  font-weight: 700;
}

.qty {
  display: inline-flex;
  align-items: center;
  gap: 8px;
}

.qty-btn {
  width: 34px;
  height: 34px;
  border-radius: 8px;
  border: 1px solid #d1d5db;
  background: #fff;
  cursor: pointer;
  font-weight: 800;
}

.qty-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.qty-input {
  width: 56px;
  height: 34px;
  text-align: center;
  border-radius: 8px;
  border: 1px solid #d1d5db;
  outline: none;
}

.x {
  width: 36px;
  height: 36px;
  border-radius: 10px;
  border: 1px solid #e5e7eb;
  background: #fff;
  cursor: pointer;
  font-size: 20px;
  line-height: 34px;
}

.empty {
  padding: 18px 12px;
  border: 1px solid #e5e7eb;
  background: #fff;
  color: #6b7280;
}

/* 底部结算条 */
.bar {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  border-top: 1px solid #e5e7eb;
  background: #fff;
}

.bar > div,
.bar {
  box-sizing: border-box;
}

.bar {
  display: flex;
  justify-content: center;
}

.bar-left,
.bar-right {
  width: 980px;
  max-width: calc(100% - 32px);
  padding: 12px 16px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
}

.bar-left {
  color: #374151;
}

.link {
  border: none;
  background: transparent;
  color: #2563eb;
  cursor: pointer;
  padding: 6px 8px;
}

.link:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.sum {
  font-weight: 800;
  color: #111827;
}

.money {
  color: #ff5000;
  font-size: 20px;
}

.checkout {
  height: 40px;
  padding: 0 18px;
  border: none;
  border-radius: 10px;
  background: #ff5000;
  color: #fff;
  font-weight: 800;
  cursor: pointer;
}

.checkout:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* 小屏优化 */
@media (max-width: 760px) {
  .table {
    grid-template-columns: 70px 1fr 90px;
  }
  .c-price,
  .c-sub,
  .c-op,
  .c-qty {
    grid-column: 2 / 4;
  }
  .cell {
    border-right: none;
  }
}
</style>