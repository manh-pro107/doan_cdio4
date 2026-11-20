<template>
  <div class="dashboard">
    <!-- thanh tải -->
    <div v-if="dangTai" class="loading-bar"></div>

    <!-- HEADER -->
    <div class="dashboard-heading">
      <div>
        <div class="eyebrow">TECHSHOP ADMINISTRATION</div>
        <h2>Tổng quan hệ thống</h2>
        <p>Theo dõi và quản lý hoạt động kinh doanh TechShop.</p>
      </div>

      <div class="heading-right">
        <div class="date-box">
          <i class="fa-regular fa-calendar"></i>
          {{ ngayHienTai }}
        </div>

        <div
          class="conn-pill"
          :class="{ ok: daKetNoi === true, fail: daKetNoi === false }"
        >
          <span class="conn-dot"></span>
          {{
            daKetNoi === true
              ? "Đã kết nối"
              : daKetNoi === false
                ? "Mất kết nối"
                : "Đang kết nối"
          }}
        </div>

        <button class="refresh-btn" @click="layThongKe" :disabled="dangTai">
          <i class="fa-solid fa-rotate" :class="{ spinning: dangTai }"></i>
          Làm mới
        </button>
      </div>
    </div>

    <!-- LỖI -->
    <div v-if="loi" class="error-banner">
      <i class="fa-solid fa-circle-exclamation"></i>
      <span>{{ loi }}</span>
      <button @click="layThongKe">Thử lại</button>
    </div>

    <!-- KPI -->
    <div class="stats-grid">
      <div
        class="stat-card tilt"
        v-for="card in kpiCards"
        :key="card.key"
        :class="card.tone"
        @mousemove="tilt($event, 9)"
        @mouseleave="resetTilt($event)"
      >
        <div class="stat-top">
          <div class="stat-icon">
            <i :class="card.icon"></i>
          </div>
          <span class="stat-label">{{ card.label }}</span>
        </div>
        <h3>{{ formatNumber(hienThi[card.key]) }}</h3>
        <p>{{ card.desc }}</p>
        <div class="stat-bottom">
          <i :class="card.footIcon"></i>
          {{ card.foot }}
        </div>
      </div>
    </div>

    <!-- HÀNG 1: DOANH THU + CỘT 3D -->
    <div class="main-grid">
      <!-- DOANH THU -->
      <section
        class="panel revenue-panel tilt-soft"
        @mousemove="tilt($event, 2.5)"
        @mouseleave="resetTilt($event)"
      >
        <div class="panel-heading">
          <div>
            <div class="section-label">BUSINESS ANALYTICS</div>
            <h3>Phân tích doanh thu</h3>
            <p>Doanh thu từ đơn đã giao, 6 tháng gần nhất</p>
          </div>

          <div class="chart-legend">
            <span class="legend-dot"></span>
            Doanh thu
          </div>
        </div>

        <div class="revenue-summary">
          <div>
            <span class="muted-label">Tổng doanh thu tích lũy</span>
            <h2>{{ formatMoney(hienThi.doanhThu) }}</h2>
          </div>
          <div class="revenue-badge">
            <i class="fa-solid fa-chart-line"></i>
            {{ coDoanhThu ? "Đơn đã giao thành công" : "Chưa có doanh thu" }}
          </div>
        </div>

        <div class="chart-box" :key="'rev-' + animKey">
          <svg
            :viewBox="`0 0 ${rev.W} ${rev.H}`"
            class="revenue-svg"
            @mouseleave="hoverRev = null"
          >
            <defs>
              <linearGradient id="revArea" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#4f46e5" stop-opacity="0.38" />
                <stop offset="60%" stop-color="#2563eb" stop-opacity="0.1" />
                <stop offset="100%" stop-color="#06b6d4" stop-opacity="0" />
              </linearGradient>
              <linearGradient id="revStroke" x1="0" y1="0" x2="1" y2="0">
                <stop offset="0%" stop-color="#4f46e5" />
                <stop offset="55%" stop-color="#2563eb" />
                <stop offset="100%" stop-color="#06b6d4" />
              </linearGradient>
              <filter id="revGlow" x="-20%" y="-40%" width="140%" height="180%">
                <feGaussianBlur stdDeviation="7" />
              </filter>
              <filter id="dotGlow" x="-100%" y="-100%" width="300%" height="300%">
                <feGaussianBlur stdDeviation="4" />
              </filter>
            </defs>

            <!-- lưới + nhãn trục Y -->
            <g v-for="tick in revenueAxis" :key="tick.y">
              <line
                class="rev-grid"
                :x1="rev.l"
                :x2="rev.W - rev.r"
                :y1="tick.y"
                :y2="tick.y"
              />
              <text
                class="rev-axis"
                :x="rev.l - 12"
                :y="tick.y + 4"
                text-anchor="end"
              >
                {{ tick.label }}
              </text>
            </g>

            <!-- vùng tô -->
            <path class="rev-area" :d="revenueArea" fill="url(#revArea)" />

            <!-- bóng đổ tạo chiều sâu -->
            <path
              class="rev-shadow"
              :d="revenuePath"
              pathLength="1"
              fill="none"
              stroke="#4f46e5"
              stroke-width="9"
              stroke-linecap="round"
              filter="url(#revGlow)"
              transform="translate(0 12)"
            />

            <!-- dải ruy băng phía sau -->
            <path
              class="rev-ribbon"
              :d="revenuePath"
              pathLength="1"
              fill="none"
              stroke="#312e81"
              stroke-width="7"
              stroke-linecap="round"
              transform="translate(0 5)"
            />

            <!-- đường chính -->
            <path
              class="rev-line"
              :d="revenuePath"
              pathLength="1"
              fill="none"
              stroke="url(#revStroke)"
              stroke-width="5"
              stroke-linecap="round"
              stroke-linejoin="round"
            />

            <!-- chấm sáng chạy dọc đường -->
            <g v-if="revenuePath" class="rev-runner">
              <circle r="9" fill="#06b6d4" filter="url(#dotGlow)" opacity="0.8">
                <animateMotion
                  :path="revenuePath"
                  dur="5s"
                  begin="2.2s"
                  repeatCount="indefinite"
                />
              </circle>
              <circle r="4" fill="#fff">
                <animateMotion
                  :path="revenuePath"
                  dur="5s"
                  begin="2.2s"
                  repeatCount="indefinite"
                />
              </circle>
            </g>

            <!-- đường dóng khi rê chuột -->
            <line
              v-if="hoverRev !== null && revenuePoints[hoverRev]"
              class="rev-guide"
              :x1="revenuePoints[hoverRev].x"
              :x2="revenuePoints[hoverRev].x"
              :y1="rev.t"
              :y2="rev.H - rev.b"
            />

            <!-- các điểm -->
            <g v-for="(p, i) in revenuePoints" :key="'pt' + i">
              <circle
                v-if="p.peak"
                class="rev-peak-ring"
                :cx="p.x"
                :cy="p.y"
                r="9"
              />
              <circle
                class="rev-point"
                :class="{ active: hoverRev === i }"
                :style="{ animationDelay: 0.5 + i * 0.18 + 's' }"
                :cx="p.x"
                :cy="p.y"
                :r="hoverRev === i ? 8 : 5.5"
              />
              <text
                class="rev-axis rev-axis-x"
                :class="{ active: hoverRev === i }"
                :x="p.x"
                :y="rev.H - 12"
                text-anchor="middle"
              >
                {{ p.label }}
              </text>
            </g>

            <!-- vùng bắt chuột -->
            <rect
              v-for="(p, i) in revenuePoints"
              :key="'hit' + i"
              class="rev-hit"
              :x="p.x - colWidth / 2"
              :y="rev.t"
              :width="colWidth"
              :height="rev.H - rev.t - rev.b"
              @mouseenter="hoverRev = i"
            />
          </svg>

          <!-- tooltip -->
          <transition name="pop">
            <div
              v-if="hoverRev !== null && revenuePoints[hoverRev]"
              class="rev-tooltip"
              :style="{
                left: (revenuePoints[hoverRev].x / rev.W) * 100 + '%',
                top: (revenuePoints[hoverRev].y / rev.H) * 100 + '%',
              }"
            >
              <span class="tip-month">Tháng {{ revenuePoints[hoverRev].full }}</span>
              <strong>{{ formatMoney(revenuePoints[hoverRev].value) }}</strong>
              <small>
                {{ formatNumber(donThang[hoverRev] ? donThang[hoverRev].value : 0) }}
                đơn hàng trong tháng
              </small>
            </div>
          </transition>

          <div v-if="!coDoanhThu && duLieu" class="chart-empty-hint">
            <i class="fa-solid fa-chart-area"></i>
            Chưa có đơn đã giao trong 6 tháng gần nhất
          </div>
        </div>

        <div class="chart-footer">
          <span>
            <i class="fa-solid fa-circle-info"></i>
            Doanh thu chỉ tính các đơn có trạng thái đã giao.
          </span>
          <span>6 tháng gần nhất</span>
        </div>
      </section>

      <!-- CỘT 3D -->
      <section class="panel order-panel">
        <div class="panel-heading">
          <div>
            <div class="section-label">ORDER ANALYTICS</div>
            <h3>Đơn hàng theo tháng</h3>
            <p>Di chuột vào cột để xem số đơn từng tháng</p>
          </div>
          <i class="fa-solid fa-cube heading-icon"></i>
        </div>

        <div class="order-total">
          <h2>{{ formatNumber(hienThi.donHang) }}</h2>
          <span>Tổng đơn hàng</span>
        </div>

        <div class="bars-chart" :key="'bars-' + animKey">
          <div class="bars-axis">
            <div
              v-for="t in barAxis"
              :key="'a' + t.value"
              class="bars-tick"
              :style="{ bottom: t.bottom + 'px' }"
            >
              {{ t.value }}
            </div>
          </div>

          <div class="bars-plot">
            <div
              v-for="t in barAxis"
              :key="'g' + t.value"
              class="bars-gridline"
              :class="{ base: t.value === 0 }"
              :style="{ bottom: t.bottom + 'px' }"
            ></div>

            <div class="bars-cols">
              <div
                class="bcol"
                v-for="(b, i) in barItems"
                :key="b.label"
                :class="{ peak: b.peak, zero: b.value === 0 }"
                :style="{ '--h': b.height + 'px', '--delay': i * 0.1 + 's' }"
              >
                <div class="bar-track"></div>
                <div class="bar">
                  <span class="bar-value">
                    <i v-if="b.peak" class="fa-solid fa-crown"></i>
                    {{ b.value }}
                  </span>
                </div>
                <span class="bar-label">{{ b.label }}</span>
              </div>
            </div>
          </div>
        </div>

        <div class="chart-footer">
          <span>
            <i class="fa-solid fa-chart-simple"></i>
            Số đơn theo ngày đặt hàng
          </span>
          <span>6 tháng gần nhất</span>
        </div>
      </section>
    </div>

    <!-- HÀNG 2 -->
    <div class="bottom-grid">
      <!-- DONUT 3D -->
      <section class="panel">
        <div class="panel-heading">
          <div>
            <div class="section-label">ORDER OVERVIEW</div>
            <h3>Trạng thái đơn hàng</h3>
            <p>Vòng tròn 3D tự xoay, rê chuột để dừng</p>
          </div>
          <i class="fa-solid fa-chart-pie heading-icon"></i>
        </div>

        <div
          class="donut-scene"
          :key="'donut-' + animKey"
          @mousemove="tilt($event, 10)"
          @mouseleave="resetTilt($event); hoverStatus = null"
        >
          <div class="donut-tilt">
            <div class="donut-spin">
              <svg
                v-for="n in donutLayers"
                :key="n"
                viewBox="0 0 200 200"
                class="donut-layer"
                :class="{ top: n === 1 }"
                :style="{
                  transform: 'translateZ(' + -(n - 1) * 1.7 + 'px)',
                  filter: n === 1 ? 'none' : 'brightness(' + (1 - (n - 1) * 0.045) + ')',
                }"
              >
                <circle
                  class="donut-track"
                  cx="100"
                  cy="100"
                  r="70"
                  fill="none"
                  stroke-width="34"
                />
                <circle
                  v-for="(s, i) in donutSegments"
                  :key="s.key"
                  class="donut-seg"
                  cx="100"
                  cy="100"
                  r="70"
                  fill="none"
                  :stroke="s.color"
                  :stroke-width="n === 1 && hoverStatus === i ? 42 : 34"
                  :stroke-dasharray="s.dash"
                  :stroke-dashoffset="s.offset"
                  transform="rotate(-90 100 100)"
                  @mouseenter="n === 1 && (hoverStatus = i)"
                  @mouseleave="n === 1 && (hoverStatus = null)"
                />
              </svg>
            </div>
          </div>

          <div class="donut-center">
            <strong>
              {{
                hoverStatus !== null && donutSegments[hoverStatus]
                  ? formatNumber(donutSegments[hoverStatus].value)
                  : formatNumber(donutTotal)
              }}
            </strong>
            <span>
              {{
                hoverStatus !== null && donutSegments[hoverStatus]
                  ? donutSegments[hoverStatus].label
                  : "Đơn hàng"
              }}
            </span>
          </div>
        </div>

        <div class="status-legend">
          <div
            v-for="(s, i) in donutSegments"
            :key="s.key"
            class="legend-row"
            :class="{ active: hoverStatus === i }"
            @mouseenter="hoverStatus = i"
            @mouseleave="hoverStatus = null"
          >
            <span class="legend-color" :style="{ background: s.color }"></span>
            <span class="legend-name">{{ s.label }}</span>
            <strong>{{ formatNumber(s.value) }}</strong>
            <em>{{ s.percent }}%</em>
          </div>
        </div>
      </section>

      <!-- KHO -->
      <section class="panel">
        <div class="panel-heading">
          <div>
            <div class="section-label">INVENTORY MANAGEMENT</div>
            <h3>Tình hình tồn kho</h3>
            <p>Kho hàng TECHSHOP - Đà Nẵng</p>
          </div>
          <i class="fa-solid fa-warehouse heading-icon"></i>
        </div>

        <div class="inventory-content" :key="'inv-' + animKey">
          <div class="gauge-row">
            <div class="gauge-wrap">
              <svg viewBox="0 0 140 140" class="gauge">
                <defs>
                  <linearGradient id="gaugeGrad" x1="0" y1="0" x2="1" y2="1">
                    <stop offset="0%" stop-color="#06b6d4" />
                    <stop offset="50%" stop-color="#2563eb" />
                    <stop offset="100%" stop-color="#4f46e5" />
                  </linearGradient>
                  <filter id="gaugeGlow" x="-30%" y="-30%" width="160%" height="160%">
                    <feGaussianBlur stdDeviation="4" />
                  </filter>
                </defs>
                <circle class="gauge-bg" cx="70" cy="70" r="54" fill="none" stroke-width="14" />
                <circle
                  class="gauge-glow"
                  cx="70"
                  cy="70"
                  r="54"
                  fill="none"
                  stroke="url(#gaugeGrad)"
                  stroke-width="14"
                  stroke-linecap="round"
                  :stroke-dasharray="gaugeCirc"
                  :stroke-dashoffset="gaugeOffset"
                  transform="rotate(-90 70 70)"
                  filter="url(#gaugeGlow)"
                />
                <circle
                  class="gauge-fill"
                  cx="70"
                  cy="70"
                  r="54"
                  fill="none"
                  stroke="url(#gaugeGrad)"
                  stroke-width="14"
                  stroke-linecap="round"
                  :stroke-dasharray="gaugeCirc"
                  :stroke-dashoffset="gaugeOffset"
                  transform="rotate(-90 70 70)"
                />
              </svg>
              <div class="gauge-center">
                <strong>{{ Math.round(hienThi.khaDung) }}%</strong>
                <span>khả dụng</span>
              </div>
            </div>

            <div class="inventory-number">
              <span>Tổng số lượng tồn kho</span>
              <h2>{{ formatNumber(hienThi.tonKho) }}</h2>
              <div class="inventory-icon">
                <i class="fa-solid fa-boxes-stacked"></i>
              </div>
            </div>
          </div>

          <div class="tube-row">
            <div class="tube-info">
              <span>Đang có sẵn</span>
              <strong>{{ formatNumber(kho.TongSoLuongKhaDung) }}</strong>
            </div>
            <div class="tube">
              <div class="tube-fill available" :style="{ width: hienThi.khaDung + '%' }"></div>
            </div>
          </div>

          <div class="tube-row">
            <div class="tube-info">
              <span>Đang được giữ</span>
              <strong>{{ formatNumber(kho.TongSoLuongDangGiu) }}</strong>
            </div>
            <div class="tube">
              <div class="tube-fill reserved" :style="{ width: hienThi.dangGiu + '%' }"></div>
            </div>
          </div>

          <div class="stock-chips">
            <div class="chip warn">
              <i class="fa-solid fa-triangle-exclamation"></i>
              <span>Sắp hết hàng</span>
              <strong>{{ formatNumber(kho.SoSanPhamSapHetHang) }}</strong>
            </div>
            <div class="chip danger">
              <i class="fa-solid fa-circle-xmark"></i>
              <span>Hết hàng</span>
              <strong>{{ formatNumber(kho.SoSanPhamHetHang) }}</strong>
            </div>
          </div>

          <div class="inventory-note">
            <i class="fa-solid fa-circle-info"></i>
            Sắp hết hàng khi số lượng khả dụng từ 1 đến
            {{ kho.NguongSapHet || 5 }}. Số liệu lấy từ bảng tồn kho (kho chính).
          </div>
        </div>
      </section>

      <!-- TRUY CẬP NHANH -->
      <section class="panel">
        <div class="panel-heading">
          <div>
            <div class="section-label">QUICK ACCESS</div>
            <h3>Quản lý nhanh</h3>
            <p>Truy cập các chức năng quản trị</p>
          </div>
          <i class="fa-solid fa-bolt heading-icon"></i>
        </div>

        <div class="quick-grid">
          <router-link to="/admin/sanpham" class="quick-item">
            <i class="fa-solid fa-box"></i>
            <span>Sản phẩm</span>
            <i class="fa-solid fa-arrow-up-right-from-square arrow"></i>
          </router-link>

          <router-link to="/admin/danhmuc" class="quick-item">
            <i class="fa-solid fa-list"></i>
            <span>Danh mục</span>
            <i class="fa-solid fa-arrow-up-right-from-square arrow"></i>
          </router-link>

          <router-link to="/admin/donhang" class="quick-item">
            <i class="fa-solid fa-cart-shopping"></i>
            <span>Đơn hàng</span>
            <i class="fa-solid fa-arrow-up-right-from-square arrow"></i>
          </router-link>

          <router-link to="/admin/voucher" class="quick-item">
            <i class="fa-solid fa-ticket"></i>
            <span>Voucher</span>
            <i class="fa-solid fa-arrow-up-right-from-square arrow"></i>
          </router-link>
        </div>
      </section>
    </div>

    <div class="dashboard-footer">
      <span>TECHSHOP ADMIN</span>
      <span>Hệ thống quản lý bán hàng</span>
    </div>
  </div>
</template>

<script>
import axios from "axios";

const API = "http://127.0.0.1:8000/api";

// Thứ tự và màu của các trạng thái đơn (khóa trùng với Controller)
const STATUS_META = [
  { key: "ChoXacNhan", label: "Chờ xác nhận", color: "#f59e0b" },
  { key: "DangXuLy", label: "Đang xử lý", color: "#2563eb" },
  { key: "DangGiao", label: "Đang giao", color: "#a855f7" },
  { key: "HoanThanh", label: "Hoàn thành", color: "#10b981" },
  { key: "DaHuy", label: "Đã hủy", color: "#f43f5e" },
];

export default {
  name: "TongQuan",

  data() {
    return {
      dangTai: false,
      loi: "",
      daKetNoi: null,
      ngayHienTai: new Date().toLocaleDateString("vi-VN"),

      // dữ liệu thật từ /admin/dashboard/thong-ke
      duLieu: null,

      // giá trị hiển thị (chạy từ 0 lên khi tải)
      hienThi: {
        sanPham: 0,
        donHang: 0,
        nguoiDung: 0,
        voucher: 0,
        doanhThu: 0,
        tonKho: 0,
        khaDung: 0,
        dangGiu: 0,
      },

      animKey: 0,
      rafIds: {},

      hoverRev: null,
      hoverStatus: null,

      // kích thước hệ trục biểu đồ doanh thu
      rev: { W: 720, H: 290, l: 66, r: 28, t: 28, b: 40 },

      donutLayers: 14,
      gaugeCirc: 2 * Math.PI * 54,
    };
  },

  computed: {
    kpiCards() {
      return [
        {
          key: "sanPham",
          tone: "tone-indigo",
          icon: "fa-solid fa-box",
          label: "SẢN PHẨM",
          desc: "Tổng sản phẩm trong hệ thống",
          footIcon: "fa-solid fa-boxes-stacked",
          foot: "Danh mục sản phẩm",
        },
        {
          key: "donHang",
          tone: "tone-purple",
          icon: "fa-solid fa-cart-shopping",
          label: "ĐƠN HÀNG",
          desc: "Tổng đơn hàng được ghi nhận",
          footIcon: "fa-solid fa-receipt",
          foot: "Quản lý đơn hàng",
        },
        {
          key: "nguoiDung",
          tone: "tone-green",
          icon: "fa-solid fa-users",
          label: "NGƯỜI DÙNG",
          desc: "Tổng tài khoản hệ thống",
          footIcon: "fa-solid fa-user-check",
          foot: "Quản lý tài khoản",
        },
        {
          key: "voucher",
          tone: "tone-orange",
          icon: "fa-solid fa-ticket",
          label: "VOUCHER",
          desc: "Tổng mã ưu đãi hiện có",
          footIcon: "fa-solid fa-tags",
          foot: "Khuyến mãi",
        },
      ];
    },

    kho() {
      return this.duLieu?.thongKeKho || {};
    },

    // ---------- doanh thu ----------
    doanhThuThang() {
      return (this.duLieu?.doanhThuTheoThang || []).map((item) => {
        const so = String(item.thang || "").replace(/\D/g, "");
        return {
          label: "T" + so,
          full: `${so}/${item.nam}`,
          value: Number(item.doanhThu) || 0,
        };
      });
    },

    donThang() {
      return (this.duLieu?.donHangTheoThang || []).map((item) => ({
        value: Number(item.soDon) || 0,
      }));
    },

    coDoanhThu() {
      return this.doanhThuThang.some((item) => item.value > 0);
    },

    revenueScale() {
      const max = Math.max(1, ...this.doanhThuThang.map((i) => i.value));
      return max * 1.15;
    },

    colWidth() {
      const n = Math.max(1, this.doanhThuThang.length);
      return (this.rev.W - this.rev.l - this.rev.r) / n;
    },

    revenuePoints() {
      const list = this.doanhThuThang;
      const { W, H, l, r, t, b } = this.rev;
      const innerW = W - l - r;
      const innerH = H - t - b;
      const max = Math.max(0, ...list.map((i) => i.value));

      return list.map((item, index) => ({
        ...item,
        x: list.length === 1 ? l + innerW / 2 : l + (index * innerW) / (list.length - 1),
        y: t + (1 - item.value / this.revenueScale) * innerH,
        peak: max > 0 && item.value === max,
      }));
    },

    // đường cong mượt (Catmull-Rom -> Bezier)
    revenuePath() {
      const p = this.revenuePoints;
      if (!p.length) return "";
      if (p.length === 1) return `M ${p[0].x},${p[0].y}`;

      const base = this.rev.H - this.rev.b;
      const top = this.rev.t;
      const clamp = (y) => Math.min(base, Math.max(top, y));

      let d = `M ${p[0].x},${p[0].y}`;

      for (let i = 0; i < p.length - 1; i++) {
        const p0 = p[i - 1] || p[i];
        const p1 = p[i];
        const p2 = p[i + 1];
        const p3 = p[i + 2] || p2;

        const c1x = p1.x + (p2.x - p0.x) / 6;
        const c1y = clamp(p1.y + (p2.y - p0.y) / 6);
        const c2x = p2.x - (p3.x - p1.x) / 6;
        const c2y = clamp(p2.y - (p3.y - p1.y) / 6);

        d += ` C ${c1x},${c1y} ${c2x},${c2y} ${p2.x},${p2.y}`;
      }

      return d;
    },

    revenueArea() {
      const p = this.revenuePoints;
      if (p.length < 2) return "";
      const base = this.rev.H - this.rev.b;
      return `${this.revenuePath} L ${p[p.length - 1].x},${base} L ${p[0].x},${base} Z`;
    },

    revenueAxis() {
      const { t, H, b } = this.rev;
      const innerH = H - t - b;

      return [0, 1, 2, 3, 4].map((i) => ({
        y: t + (innerH * i) / 4,
        label: this.formatCompact((this.revenueScale * (4 - i)) / 4),
      }));
    },

    // ---------- cột 3D ----------
    barItems() {
      const list = this.doanhThuThang.length
        ? this.doanhThuThang
        : (this.duLieu?.donHangTheoThang || []).map((x) => ({
            label: "T" + String(x.thang || "").replace(/\D/g, ""),
          }));

      const values = (this.duLieu?.donHangTheoThang || []).map(
        (x) => Number(x.soDon) || 0
      );
      const max = Math.max(1, ...values);

      return list.map((item, i) => {
        const value = values[i] || 0;
        return {
          label: item.label,
          value,
          height: value === 0 ? 5 : Math.max(14, (value / max) * 150),
          peak: value > 0 && value === Math.max(...values),
        };
      });
    },

    // trục chia vạch cho biểu đồ cột
    barAxis() {
      const values = (this.duLieu?.donHangTheoThang || []).map(
        (x) => Number(x.soDon) || 0
      );
      const max = Math.max(1, ...values);
      const ticks =
        max > 2 ? [max, Math.round(max / 2), 0] : max === 2 ? [2, 1, 0] : [1, 0];

      return ticks.map((v) => ({ value: v, bottom: (v / max) * 150 }));
    },

    // ---------- donut 3D ----------
    donutTotal() {
      const s = this.duLieu?.donHangTheoTrangThai || {};
      return STATUS_META.reduce((sum, m) => sum + (Number(s[m.key]) || 0), 0);
    },

    donutSegments() {
      const s = this.duLieu?.donHangTheoTrangThai || {};
      const total = this.donutTotal;
      const C = 2 * Math.PI * 70;
      const gap = total > 0 ? 3 : 0;

      let cumulative = 0;

      return STATUS_META.map((m) => {
        const value = Number(s[m.key]) || 0;
        const fraction = total ? value / total : 0;
        const length = Math.max(0, fraction * C - (value ? gap : 0));

        const seg = {
          ...m,
          value,
          percent: total ? Math.round(fraction * 100) : 0,
          dash: `${length} ${C - length}`,
          offset: -cumulative * C,
        };

        cumulative += fraction;
        return seg;
      });
    },

    // ---------- kho ----------
    gaugeOffset() {
      return this.gaugeCirc * (1 - Math.min(100, this.hienThi.khaDung) / 100);
    },
  },

  mounted() {
    this.layThongKe();
  },

  beforeUnmount() {
    Object.values(this.rafIds).forEach((id) => cancelAnimationFrame(id));
  },

  methods: {
    getConfig() {
      const token =
        localStorage.getItem("admin_token") ||
        localStorage.getItem("adminToken") ||
        localStorage.getItem("token");

      return {
        headers: {
          Accept: "application/json",
          ...(token ? { Authorization: `Bearer ${token}` } : {}),
        },
      };
    },

    async layThongKe() {
      if (this.dangTai) return;

      this.dangTai = true;
      this.loi = "";

      try {
        const res = await axios.get(
          `${API}/admin/dashboard/thong-ke`,
          this.getConfig()
        );

        if (!res.data?.status || !res.data?.data) {
          throw new Error(res.data?.message || "Không lấy được thống kê.");
        }

        this.duLieu = res.data.data;
        this.daKetNoi = true;
        this.animKey++;
        this.hoverRev = null;
        this.hoverStatus = null;

        this.chayHieuUng();
      } catch (error) {
        console.error("Lỗi thống kê dashboard:", error);
        this.daKetNoi = false;
        this.loi =
          error.response?.data?.message ||
          error.message ||
          "Không thể kết nối API thống kê.";
      } finally {
        this.dangTai = false;
      }
    },

    // chạy số từ 0 lên giá trị thật
    chayHieuUng() {
      const d = this.duLieu || {};
      const k = d.thongKeKho || {};
      const ton = Number(k.TongSoLuongTon) || 0;
      const kha = Number(k.TongSoLuongKhaDung) || 0;
      const giu = Number(k.TongSoLuongDangGiu) || 0;

      this.demLen("sanPham", Number(d.tongSanPham) || 0);
      this.demLen("donHang", Number(d.tongDonHang) || 0);
      this.demLen("nguoiDung", Number(d.tongNguoiDung) || 0);
      this.demLen("voucher", Number(d.tongVoucher) || 0);
      this.demLen("doanhThu", Number(d.tongDoanhThu) || 0, 1800);
      this.demLen("tonKho", ton);
      this.demLen("khaDung", ton ? (kha / ton) * 100 : 0, 1600);
      this.demLen("dangGiu", ton ? (giu / ton) * 100 : 0, 1600);
    },

    demLen(key, dich, thoiGian = 1300) {
      cancelAnimationFrame(this.rafIds[key]);
      const batDau = performance.now();

      const buoc = (now) => {
        const t = Math.min(1, (now - batDau) / thoiGian);
        const ease = 1 - Math.pow(1 - t, 3);
        this.hienThi[key] = dich * ease;

        if (t < 1) {
          this.rafIds[key] = requestAnimationFrame(buoc);
        } else {
          this.hienThi[key] = dich;
        }
      };

      this.hienThi[key] = 0;
      this.rafIds[key] = requestAnimationFrame(buoc);
    },

    // nghiêng 3D theo vị trí chuột
    tilt(event, max = 8) {
      const el = event.currentTarget;
      const rect = el.getBoundingClientRect();
      const x = (event.clientX - rect.left) / rect.width - 0.5;
      const y = (event.clientY - rect.top) / rect.height - 0.5;

      el.style.setProperty("--ry", (x * max).toFixed(2) + "deg");
      el.style.setProperty("--rx", (-y * max).toFixed(2) + "deg");
      el.style.setProperty("--mx", ((x + 0.5) * 100).toFixed(1) + "%");
      el.style.setProperty("--my", ((y + 0.5) * 100).toFixed(1) + "%");
    },

    resetTilt(event) {
      const el = event.currentTarget;
      el.style.setProperty("--ry", "0deg");
      el.style.setProperty("--rx", "0deg");
    },

    formatNumber(value) {
      return Math.round(Number(value) || 0).toLocaleString("vi-VN");
    },

    formatMoney(value) {
      return Math.round(Number(value) || 0).toLocaleString("vi-VN") + " đ";
    },

    formatCompact(value) {
      if (value >= 1000000000) return (value / 1000000000).toFixed(1) + " tỷ";
      if (value >= 1000000) return (value / 1000000).toFixed(0) + " tr";
      if (value >= 1000) return (value / 1000).toFixed(0) + " k";
      return Math.round(value).toString();
    },
  },
};
</script>

<style scoped>
/* =========================================================
   BIẾN & NỀN
   ========================================================= */
.dashboard {
  --primary: #4f46e5;
  --primary-2: #2563eb;
  --accent: #06b6d4;
  --navy: #0f172a;
  --text: #1e293b;
  --muted: #64748b;
  --line: #e2e8f0;
  --grad-primary: linear-gradient(135deg, #4f46e5 0%, #2563eb 55%, #06b6d4 100%);
  --shadow-sm: 0 2px 8px rgba(15, 23, 42, 0.06);
  --ease: cubic-bezier(0.22, 1, 0.36, 1);

  position: relative;
  width: 100%;
  padding-bottom: 20px;
  color: var(--text);
  font-family: "Roboto", "Inter", Arial, sans-serif;
}

.dashboard * {
  box-sizing: border-box;
}

/* =========================================================
   KEYFRAMES
   ========================================================= */
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(18px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes spin { to { transform: rotate(360deg); } }

@keyframes shine {
  from { transform: translateX(-120%) skewX(-20deg); }
  to { transform: translateX(260%) skewX(-20deg); }
}

@keyframes pulseDot {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.4); }
}

@keyframes ping {
  0% { box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.55); }
  100% { box-shadow: 0 0 0 9px rgba(16, 185, 129, 0); }
}

@keyframes loadingSlide {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}

@keyframes drawLine { to { stroke-dashoffset: 0; } }
@keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

@keyframes pointPop {
  0% { opacity: 0; transform: scale(0); }
  70% { opacity: 1; transform: scale(1.4); }
  100% { opacity: 1; transform: scale(1); }
}

@keyframes ringPulse {
  0% { opacity: 0.7; transform: scale(0.6); }
  100% { opacity: 0; transform: scale(2.4); }
}

@keyframes barGrow { from { height: 0; opacity: 0; } }

@keyframes barFloat {
  0%, 100% { transform: translateZ(0); }
  50% { transform: translateZ(9px); }
}

@keyframes donutSpin {
  from { transform: rotateZ(0deg); }
  to { transform: rotateZ(360deg); }
}

@keyframes donutIn {
  from { opacity: 0; transform: scale(0.6) rotateZ(-90deg); }
  to { opacity: 1; transform: scale(1) rotateZ(0deg); }
}

@keyframes floatIcon {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-4px); }
}

@keyframes growX { from { transform: scaleX(0); } }

@keyframes tubeShine {
  from { background-position: -120% 0; }
  to { background-position: 220% 0; }
}

.dashboard-heading { animation: fadeUp 0.5s var(--ease) both; }

.stat-card { animation: fadeUp 0.55s var(--ease) both; }
.stat-card:nth-child(1) { animation-delay: 0.05s; }
.stat-card:nth-child(2) { animation-delay: 0.12s; }
.stat-card:nth-child(3) { animation-delay: 0.19s; }
.stat-card:nth-child(4) { animation-delay: 0.26s; }

.revenue-panel { animation: fadeUp 0.6s var(--ease) 0.3s both; }
.order-panel { animation: fadeUp 0.6s var(--ease) 0.38s both; }

.bottom-grid .panel { animation: fadeUp 0.6s var(--ease) both; }
.bottom-grid .panel:nth-child(1) { animation-delay: 0.42s; }
.bottom-grid .panel:nth-child(2) { animation-delay: 0.48s; }
.bottom-grid .panel:nth-child(3) { animation-delay: 0.54s; }

/* =========================================================
   THANH TẢI / LỖI
   ========================================================= */
.loading-bar {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 3000;
  width: 100%;
  height: 3px;
  overflow: hidden;
  background: rgba(79, 70, 229, 0.12);
}

.loading-bar::after {
  content: "";
  display: block;
  width: 60%;
  height: 100%;
  background: var(--grad-primary);
  animation: loadingSlide 1.1s ease-in-out infinite;
}

.error-banner {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 18px;
  padding: 13px 18px;
  border: 1px solid #fecdd3;
  border-left: 5px solid #f43f5e;
  border-radius: 12px;
  color: #be123c;
  background: #fff1f2;
  font-size: 13px;
  font-weight: 600;
  animation: fadeUp 0.4s var(--ease) both;
}

.error-banner button {
  margin-left: auto;
  padding: 7px 14px;
  border: 0;
  border-radius: 9px;
  color: #fff;
  background: linear-gradient(135deg, #f43f5e, #e11d48);
  font-size: 12px;
  font-weight: 700;
  cursor: pointer;
}

/* =========================================================
   HEADER
   ========================================================= */
.dashboard-heading {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
  margin-bottom: 26px;
}

.eyebrow,
.section-label {
  display: inline-flex;
  margin-bottom: 9px;
  padding: 4px 12px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 10px;
  font-weight: 800;
  letter-spacing: 1.3px;
}

.dashboard-heading h2 {
  margin: 0 0 7px;
  font-size: 30px;
  font-weight: 800;
  letter-spacing: -0.5px;
  background: linear-gradient(90deg, #0f172a 0%, #4f46e5 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.dashboard-heading p,
.panel-heading p {
  margin: 0;
  color: var(--muted);
  font-size: 12.5px;
}

.heading-right {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 10px;
}

.date-box,
.conn-pill,
.refresh-btn {
  display: inline-flex;
  align-items: center;
  min-height: 42px;
  padding: 0 16px;
  border: 1.5px solid var(--line);
  border-radius: 11px;
  background: #fff;
  color: #475569;
  font-size: 12.5px;
  font-weight: 600;
}

.date-box i { margin-right: 8px; color: var(--primary); }

.conn-pill { gap: 8px; }

.conn-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #f59e0b;
  animation: pulseDot 1.2s ease-in-out infinite;
}

.conn-pill.ok { color: #047857; border-color: #a7f3d0; background: #ecfdf5; }
.conn-pill.ok .conn-dot { background: #10b981; animation: ping 1.8s ease-out infinite; }
.conn-pill.fail { color: #be123c; border-color: #fecdd3; background: #fff1f2; }
.conn-pill.fail .conn-dot { background: #f43f5e; animation: none; }

.refresh-btn {
  position: relative;
  overflow: hidden;
  border: 0;
  color: #fff;
  background: var(--grad-primary);
  background-size: 140% 140%;
  background-position: 0% 50%;
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.32);
  font-weight: 700;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease),
    background-position 0.4s;
}

.refresh-btn::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 40%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.35), transparent);
  transform: translateX(-120%) skewX(-20deg);
  pointer-events: none;
}

.refresh-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  background-position: 100% 50%;
  box-shadow: 0 12px 26px rgba(79, 70, 229, 0.42);
}

.refresh-btn:hover:not(:disabled)::after { animation: shine 0.8s var(--ease); }
.refresh-btn:disabled { opacity: 0.75; cursor: wait; }
.refresh-btn i { margin-right: 7px; }
.spinning { animation: spin 1s linear infinite; }

/* =========================================================
   KPI (NGHIÊNG 3D + ÁNH SÁNG THEO CHUỘT)
   ========================================================= */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 18px;
  margin-bottom: 22px;
}

.stat-card,
.panel {
  background: #fff;
  border: 1px solid var(--line);
  border-radius: 18px;
  box-shadow: var(--shadow-sm);
}

.stat-card {
  --tone: #4f46e5;
  --tone-soft: rgba(79, 70, 229, 0.1);
  --icon: linear-gradient(135deg, #6366f1, #4f46e5);
  --icon-shadow: rgba(79, 70, 229, 0.38);

  position: relative;
  overflow: hidden;
  padding: 20px;
  transform: perspective(900px) rotateX(var(--rx, 0deg)) rotateY(var(--ry, 0deg));
  transition: transform 0.25s ease-out, box-shadow 0.3s var(--ease),
    border-color 0.3s;
  will-change: transform;
}

.stat-card::before {
  content: "";
  position: absolute;
  top: -42px;
  right: -42px;
  width: 130px;
  height: 130px;
  border-radius: 50%;
  background: var(--tone-soft);
  transition: transform 0.5s var(--ease);
}

/* ánh sáng bám theo chuột */
.stat-card::after {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  background: radial-gradient(
    240px circle at var(--mx, 50%) var(--my, 50%),
    rgba(255, 255, 255, 0.55),
    transparent 60%
  );
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.3s;
}

.stat-card:hover {
  border-color: transparent;
  box-shadow: 0 20px 40px rgba(15, 23, 42, 0.16);
}

.stat-card:hover::before { transform: scale(1.5); }
.stat-card:hover::after { opacity: 1; }

.tone-indigo { --tone: #4f46e5; --tone-soft: rgba(79, 70, 229, 0.1); --icon: linear-gradient(135deg, #6366f1, #4f46e5); --icon-shadow: rgba(79, 70, 229, 0.38); }
.tone-purple { --tone: #a855f7; --tone-soft: rgba(168, 85, 247, 0.12); --icon: linear-gradient(135deg, #c084fc, #9333ea); --icon-shadow: rgba(168, 85, 247, 0.38); }
.tone-green { --tone: #10b981; --tone-soft: rgba(16, 185, 129, 0.12); --icon: linear-gradient(135deg, #34d399, #059669); --icon-shadow: rgba(16, 185, 129, 0.38); }
.tone-orange { --tone: #f59e0b; --tone-soft: rgba(245, 158, 11, 0.13); --icon: linear-gradient(135deg, #fbbf24, #f97316); --icon-shadow: rgba(245, 158, 11, 0.4); }

.stat-top {
  position: relative;
  z-index: 1;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.stat-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 52px;
  height: 52px;
  border-radius: 16px;
  color: #fff;
  background: var(--icon);
  box-shadow: 0 10px 20px var(--icon-shadow);
  font-size: 20px;
  transform: translateZ(30px);
  animation: floatIcon 3.2s ease-in-out infinite;
}

.stat-label {
  color: #94a3b8;
  font-size: 10px;
  font-weight: 800;
  letter-spacing: 0.9px;
}

.stat-card h3 {
  position: relative;
  z-index: 1;
  margin: 16px 0 5px;
  color: var(--navy);
  font-size: 33px;
  font-weight: 800;
  line-height: 1.1;
}

.stat-card p {
  position: relative;
  z-index: 1;
  margin: 0;
  color: var(--muted);
  font-size: 11.5px;
}

.stat-bottom {
  position: relative;
  z-index: 1;
  margin-top: 16px;
  padding-top: 12px;
  border-top: 1px dashed var(--line);
  color: var(--tone);
  font-size: 11px;
  font-weight: 700;
}

.stat-bottom i { margin-right: 5px; }

/* =========================================================
   PANEL CHUNG
   ========================================================= */
.main-grid {
  display: grid;
  grid-template-columns: 1.7fr 1fr;
  gap: 18px;
  margin-bottom: 18px;
}

.panel {
  min-width: 0;
  padding: 24px;
  transition: box-shadow 0.3s var(--ease), border-color 0.3s;
}

.panel:hover {
  border-color: #c7d2fe;
  box-shadow: 0 14px 34px rgba(79, 70, 229, 0.1);
}

.panel-heading {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 12px;
  margin-bottom: 20px;
}

.panel-heading h3 {
  margin: 0 0 6px;
  color: var(--navy);
  font-size: 17px;
  font-weight: 800;
}

.heading-icon {
  display: grid;
  place-items: center;
  width: 40px;
  height: 40px;
  border-radius: 12px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
  font-size: 16px;
  transition: transform 0.35s var(--ease);
}

.panel:hover .heading-icon { transform: rotate(-10deg) scale(1.08); }

.chart-legend {
  padding: 6px 12px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 11px;
  font-weight: 700;
  white-space: nowrap;
}

.legend-dot {
  display: inline-block;
  width: 8px;
  height: 8px;
  margin-right: 6px;
  border-radius: 50%;
  background: var(--primary);
  animation: pulseDot 1.8s ease-in-out infinite;
}

.chart-footer {
  display: flex;
  justify-content: space-between;
  gap: 10px;
  margin-top: 16px;
  padding-top: 15px;
  border-top: 1px solid var(--line);
  color: #94a3b8;
  font-size: 10.5px;
}

.chart-footer i { margin-right: 5px; color: var(--primary); }

/* =========================================================
   DOANH THU
   ========================================================= */
.revenue-panel {
  transform: perspective(1400px) rotateX(var(--rx, 0deg)) rotateY(var(--ry, 0deg));
  transition: transform 0.3s ease-out, box-shadow 0.3s var(--ease),
    border-color 0.3s;
}

.revenue-summary {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
  margin-bottom: 10px;
}

.muted-label {
  color: var(--muted);
  font-size: 11px;
  font-weight: 600;
}

.revenue-summary h2 {
  margin: 7px 0 0;
  font-size: 31px;
  font-weight: 800;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.revenue-badge {
  padding: 8px 12px;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
  font-size: 10.5px;
  font-weight: 700;
}

.chart-box {
  position: relative;
  width: 100%;
  background:
    radial-gradient(circle at 20% 0%, rgba(99, 102, 241, 0.08), transparent 55%),
    radial-gradient(circle at 90% 100%, rgba(6, 182, 212, 0.08), transparent 55%);
  border-radius: 14px;
}

.revenue-svg {
  display: block;
  width: 100%;
  height: auto;
  overflow: visible;
}

.rev-grid {
  stroke: #e2e8f0;
  stroke-dasharray: 5 6;
}

.rev-axis {
  fill: #94a3b8;
  font-size: 11px;
  font-weight: 600;
}

.rev-axis-x { transition: fill 0.2s; }
.rev-axis-x.active { fill: var(--primary); font-weight: 800; }

.rev-area { animation: fadeIn 1.2s ease 0.6s both; }

.rev-line,
.rev-shadow,
.rev-ribbon {
  stroke-dasharray: 1;
  stroke-dashoffset: 1;
  animation: drawLine 1.9s var(--ease) 0.2s forwards;
}

.rev-shadow { opacity: 0.5; }
.rev-ribbon { opacity: 0.35; }

.rev-line { filter: drop-shadow(0 4px 6px rgba(79, 70, 229, 0.35)); }

.rev-guide {
  stroke: #818cf8;
  stroke-width: 1.5;
  stroke-dasharray: 4 5;
}

.rev-point {
  fill: #fff;
  stroke: #4f46e5;
  stroke-width: 3.5;
  transform-box: fill-box;
  transform-origin: center;
  animation: pointPop 0.5s var(--ease) both;
  transition: r 0.2s var(--ease);
  filter: drop-shadow(0 3px 5px rgba(79, 70, 229, 0.4));
}

.rev-point.active {
  fill: #4f46e5;
  stroke: #fff;
}

.rev-peak-ring {
  fill: none;
  stroke: #06b6d4;
  stroke-width: 2;
  transform-box: fill-box;
  transform-origin: center;
  animation: ringPulse 2s ease-out 1.6s infinite;
}

.rev-hit {
  fill: transparent;
  cursor: pointer;
}

.rev-tooltip {
  position: absolute;
  z-index: 5;
  display: flex;
  flex-direction: column;
  gap: 3px;
  min-width: 150px;
  padding: 11px 14px;
  border-radius: 14px;
  color: #fff;
  background: linear-gradient(135deg, #1e1b4b, #312e81);
  box-shadow: 0 16px 34px rgba(30, 27, 75, 0.4);
  transform: translate(-50%, calc(-100% - 16px));
  pointer-events: none;
}

.rev-tooltip::after {
  content: "";
  position: absolute;
  bottom: -6px;
  left: 50%;
  width: 12px;
  height: 12px;
  background: #312e81;
  transform: translateX(-50%) rotate(45deg);
}

.rev-tooltip strong { font-size: 15px; font-weight: 800; }
.rev-tooltip .tip-month { color: #a5b4fc; font-size: 10.5px; font-weight: 700; }
.rev-tooltip small { color: #c7d2fe; font-size: 10.5px; }

.pop-enter-active, .pop-leave-active { transition: opacity 0.2s, margin-top 0.2s; }
.pop-enter-from, .pop-leave-to { opacity: 0; margin-top: 6px; }

.chart-empty-hint {
  position: absolute;
  left: 50%;
  top: 42%;
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 9px 16px;
  border-radius: 999px;
  color: var(--primary);
  background: #eef2ff;
  box-shadow: 0 6px 16px rgba(79, 70, 229, 0.15);
  font-size: 11.5px;
  font-weight: 700;
  transform: translateX(-50%);
  pointer-events: none;
}

/* =========================================================
   BIỂU ĐỒ CỘT
   ========================================================= */
.order-total {
  display: flex;
  align-items: baseline;
  gap: 9px;
  margin: 6px 0 4px;
}

.order-total h2 {
  margin: 0;
  font-size: 34px;
  font-weight: 800;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.order-total span {
  color: var(--muted);
  font-size: 12px;
  font-weight: 600;
}

.bars-chart {
  position: relative;
  height: 210px;
  margin-top: 6px;
}

/* trục số bên trái */
.bars-axis {
  position: absolute;
  left: 0;
  bottom: 28px;
  width: 30px;
  height: 150px;
}

.bars-tick {
  position: absolute;
  right: 0;
  color: #94a3b8;
  font-size: 10.5px;
  font-weight: 700;
  transform: translateY(50%);
}

.bars-plot {
  position: absolute;
  left: 38px;
  right: 0;
  bottom: 28px;
  height: 150px;
}

.bars-gridline {
  position: absolute;
  left: 0;
  right: 0;
  height: 0;
  border-top: 1px dashed #dbe3f0;
}

.bars-gridline.base {
  border-top: 1.5px solid #c7d2fe;
}

.bars-cols {
  position: absolute;
  inset: 0;
  display: flex;
}

.bcol {
  position: relative;
  flex: 1;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  height: 100%;
  cursor: pointer;
}

/* dải sáng nền khi rê chuột */
.bcol::before {
  content: "";
  position: absolute;
  inset: -30px 6px -28px 6px;
  border-radius: 14px;
  background: linear-gradient(180deg, rgba(99, 102, 241, 0.09), rgba(99, 102, 241, 0));
  opacity: 0;
  transition: opacity 0.25s;
}

.bcol:hover::before { opacity: 1; }

.bar-track {
  position: absolute;
  bottom: 0;
  width: 34px;
  height: 100%;
  border-radius: 12px;
  background: #f1f5ff;
}

.bar {
  position: relative;
  width: 34px;
  height: var(--h);
  border-radius: 12px 12px 6px 6px;
  background: linear-gradient(180deg, #818cf8 0%, #4f46e5 100%);
  box-shadow: 0 10px 20px rgba(79, 70, 229, 0.32);
  transform-origin: bottom;
  transition: transform 0.3s var(--ease), filter 0.3s, box-shadow 0.3s;
  animation: barGrow 1s var(--ease) var(--delay) both;
}

/* tạo khối tròn nhẹ: sáng ở giữa, tối dần hai bên */
.bar::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  background: linear-gradient(
    90deg,
    rgba(255, 255, 255, 0.34) 0%,
    rgba(255, 255, 255, 0) 38%,
    rgba(15, 23, 42, 0.14) 100%
  );
}

/* vệt sáng nhỏ ở đỉnh cột */
.bar::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 9px;
  border-radius: 12px 12px 0 0;
  background: rgba(255, 255, 255, 0.28);
}

.bcol:hover .bar {
  transform: translateY(-5px) scaleX(1.1);
  filter: brightness(1.08);
  box-shadow: 0 16px 28px rgba(79, 70, 229, 0.42);
}

.bcol.peak .bar {
  background: linear-gradient(180deg, #22d3ee 0%, #2563eb 100%);
  box-shadow: 0 10px 22px rgba(6, 182, 212, 0.38);
}

.bcol.peak:hover .bar { box-shadow: 0 16px 30px rgba(6, 182, 212, 0.5); }

/* tháng không có đơn: chỉ còn ô nền mờ */
.bcol.zero .bar {
  opacity: 0;
  box-shadow: none;
}

.bar-value {
  position: absolute;
  bottom: 100%;
  left: 50%;
  display: inline-flex;
  align-items: center;
  gap: 4px;
  margin-bottom: 9px;
  padding: 3px 10px;
  border-radius: 999px;
  color: var(--primary);
  background: #fff;
  box-shadow: 0 4px 12px rgba(79, 70, 229, 0.2);
  font-size: 11.5px;
  font-weight: 800;
  white-space: nowrap;
  transform: translateX(-50%);
  transition: transform 0.3s var(--ease);
}

.bcol:hover .bar-value { transform: translateX(-50%) scale(1.12); }

.bcol.peak .bar-value {
  color: #fff;
  background: linear-gradient(135deg, #06b6d4, #2563eb);
  box-shadow: 0 6px 14px rgba(6, 182, 212, 0.4);
}

.bcol.peak .bar-value i {
  color: #fde68a;
  font-size: 10px;
}

.bcol.zero .bar-value {
  color: #94a3b8;
  background: transparent;
  box-shadow: none;
  font-weight: 700;
}

.bar-label {
  position: absolute;
  top: 100%;
  left: 50%;
  margin-top: 9px;
  color: #94a3b8;
  font-size: 11.5px;
  font-weight: 700;
  transform: translateX(-50%);
  transition: color 0.2s;
}

.bcol:hover .bar-label { color: var(--primary); }

/* =========================================================
   LƯỚI DƯỚI
   ========================================================= */
.bottom-grid {
  display: grid;
  grid-template-columns: 1.1fr 1.1fr 0.8fr;
  gap: 18px;
}

/* ---------- donut 3D ---------- */
.donut-scene {
  position: relative;
  width: 100%;
  max-width: 270px;
  height: 200px;
  margin: 0 auto 6px;
  perspective: 900px;
}

.donut-tilt {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 230px;
  height: 230px;
  margin: -115px 0 0 -115px;
  transform-style: preserve-3d;
  transform: rotateX(calc(58deg + var(--rx, 0deg))) rotateY(var(--ry, 0deg));
  transition: transform 0.25s ease-out;
  animation: donutIn 1.2s var(--ease) both;
}

.donut-spin {
  position: absolute;
  inset: 0;
  transform-style: preserve-3d;
  animation: donutSpin 42s linear infinite;
}

.donut-scene:hover .donut-spin { animation-play-state: paused; }

.donut-layer {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  overflow: visible;
  pointer-events: none;
}

.donut-layer.top { pointer-events: auto; }

.donut-track { stroke: #eef2ff; }

.donut-seg {
  cursor: pointer;
  transition: stroke-width 0.25s var(--ease);
}

.donut-center {
  position: absolute;
  left: 50%;
  top: 44%;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  transform: translate(-50%, -50%);
  pointer-events: none;
}

.donut-center strong {
  color: var(--navy);
  font-size: 27px;
  font-weight: 800;
  line-height: 1;
  text-shadow: 0 2px 8px rgba(255, 255, 255, 0.9);
}

.donut-center span {
  margin-top: 4px;
  color: var(--muted);
  font-size: 10.5px;
  font-weight: 700;
}

.status-legend {
  display: flex;
  flex-direction: column;
  gap: 4px;
  margin-top: 8px;
}

.legend-row {
  display: flex;
  align-items: center;
  gap: 9px;
  padding: 7px 11px;
  border-radius: 10px;
  color: var(--muted);
  font-size: 12px;
  font-weight: 600;
  cursor: default;
  transition: background 0.2s, transform 0.2s var(--ease);
}

.legend-row:hover,
.legend-row.active {
  background: #f5f7ff;
  transform: translateX(5px);
}

.legend-row strong {
  margin-left: auto;
  color: var(--navy);
  font-weight: 800;
}

.legend-row em {
  min-width: 38px;
  padding: 2px 7px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 10px;
  font-style: normal;
  font-weight: 800;
  text-align: center;
}

.legend-color {
  width: 11px;
  height: 11px;
  flex-shrink: 0;
  border-radius: 4px;
  box-shadow: 0 3px 6px rgba(15, 23, 42, 0.2);
}

/* ---------- kho ---------- */
.inventory-content {
  display: flex;
  flex-direction: column;
  gap: 18px;
}

.gauge-row {
  display: flex;
  align-items: center;
  gap: 16px;
}

.gauge-wrap {
  position: relative;
  width: 140px;
  height: 140px;
  flex-shrink: 0;
  animation: fadeIn 0.8s ease both;
}

.gauge { width: 100%; height: 100%; overflow: visible; }

.gauge-bg { stroke: #eef2ff; }
.gauge-glow { opacity: 0.55; }

.gauge-center {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.gauge-center strong {
  color: var(--navy);
  font-size: 26px;
  font-weight: 800;
  line-height: 1;
}

.gauge-center span {
  margin-top: 4px;
  color: var(--muted);
  font-size: 10.5px;
  font-weight: 700;
}

.inventory-number {
  position: relative;
  min-width: 0;
}

.inventory-number span {
  color: var(--muted);
  font-size: 11.5px;
  font-weight: 600;
}

.inventory-number h2 {
  margin: 7px 0 10px;
  font-size: 30px;
  font-weight: 800;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.inventory-icon {
  display: grid;
  place-items: center;
  width: 44px;
  height: 44px;
  border-radius: 14px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.32);
  font-size: 18px;
  animation: floatIcon 3s ease-in-out infinite;
}

.tube-info {
  display: flex;
  justify-content: space-between;
  margin-bottom: 8px;
  color: var(--muted);
  font-size: 12px;
  font-weight: 600;
}

.tube-info strong { color: var(--navy); font-weight: 800; }

/* ống 3D bóng */
.tube {
  height: 16px;
  overflow: hidden;
  border-radius: 999px;
  background: linear-gradient(180deg, #dfe4f5, #f1f5ff);
  box-shadow: inset 0 3px 6px rgba(15, 23, 42, 0.16), 0 1px 0 #fff;
}

.tube-fill {
  position: relative;
  height: 100%;
  min-width: 2%;
  border-radius: 999px;
  box-shadow: 0 3px 8px rgba(79, 70, 229, 0.35);
}

.tube-fill::before {
  content: "";
  position: absolute;
  top: 2px;
  left: 6px;
  right: 6px;
  height: 4px;
  border-radius: 999px;
  background: linear-gradient(90deg, rgba(255, 255, 255, 0.6), rgba(255, 255, 255, 0.1));
}

.tube-fill::after {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  background: linear-gradient(100deg, transparent 30%, rgba(255, 255, 255, 0.45) 50%, transparent 70%);
  background-size: 60% 100%;
  background-repeat: no-repeat;
  animation: tubeShine 2.8s ease-in-out 1.4s infinite;
}

.tube-fill.available { background: linear-gradient(180deg, #34d399, #059669 70%, #047857); }
.tube-fill.reserved { background: linear-gradient(180deg, #818cf8, #4f46e5 70%, #3730a3); }

.stock-chips {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 10px;
}

.chip {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 11px 12px;
  border-radius: 14px;
  font-size: 11px;
  font-weight: 700;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease);
}

.chip:hover { transform: translateY(-3px); }
.chip span { flex: 1; }
.chip strong { font-size: 16px; font-weight: 800; }

.chip.warn { color: #b45309; background: #fef3c7; }
.chip.warn:hover { box-shadow: 0 8px 18px rgba(245, 158, 11, 0.3); }
.chip.danger { color: #be123c; background: #ffe4e6; }
.chip.danger:hover { box-shadow: 0 8px 18px rgba(244, 63, 94, 0.3); }

.inventory-note {
  padding: 12px 14px;
  border-left: 4px solid var(--primary);
  border-radius: 0 12px 12px 0;
  color: var(--muted);
  background: #f5f7ff;
  font-size: 10.5px;
  line-height: 1.7;
}

.inventory-note i { margin-right: 5px; color: var(--primary); }

/* ---------- truy cập nhanh ---------- */
.quick-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 12px;
}

.quick-item {
  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
  gap: 10px;
  min-width: 0;
  padding: 18px 14px;
  border: 1.5px solid var(--line);
  border-radius: 14px;
  color: #475569;
  background: #f8fafc;
  text-decoration: none;
  font-size: 12.5px;
  font-weight: 700;
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease),
    color 0.3s, border-color 0.3s, background 0.3s;
}

.quick-item > i:first-child {
  color: var(--primary);
  font-size: 17px;
  transition: transform 0.35s var(--ease), color 0.3s;
}

.quick-item > span { flex: 1; }

.quick-item .arrow {
  color: #94a3b8;
  font-size: 10px;
  transition: transform 0.3s var(--ease), color 0.3s;
}

.quick-item:hover {
  transform: translateY(-4px);
  border-color: transparent;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 12px 26px rgba(79, 70, 229, 0.35);
}

.quick-item:hover > i:first-child { color: #fff; transform: scale(1.2) rotate(-8deg); }
.quick-item:hover .arrow { color: #fff; transform: translate(3px, -3px); }

.dashboard-footer {
  display: flex;
  justify-content: space-between;
  margin-top: 24px;
  color: #94a3b8;
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 0.5px;
}

/* =========================================================
   RESPONSIVE
   ========================================================= */
@media (max-width: 1280px) {
  .bottom-grid { grid-template-columns: repeat(2, minmax(0, 1fr)); }
}

@media (max-width: 1200px) {
  .stats-grid { grid-template-columns: repeat(2, minmax(0, 1fr)); }
  .main-grid { grid-template-columns: 1fr; }
}

@media (max-width: 768px) {
  .dashboard-heading { align-items: flex-start; flex-direction: column; }
  .dashboard-heading h2 { font-size: 25px; }
  .bottom-grid { grid-template-columns: 1fr; }
}

@media (max-width: 480px) {
  .stats-grid { grid-template-columns: 1fr; }
  .quick-grid { grid-template-columns: 1fr; }
  .panel { padding: 16px; }
  .revenue-summary { align-items: flex-start; flex-direction: column; }
  .revenue-summary h2 { font-size: 24px; }
  .chart-footer { flex-direction: column; }
  .gauge-row { flex-direction: column; align-items: flex-start; }
}

/* Tôn trọng người dùng tắt hiệu ứng chuyển động */
@media (prefers-reduced-motion: reduce) {
  .dashboard *,
  .dashboard *::before,
  .dashboard *::after {
    animation: none !important;
    transition: none !important;
  }

  .rev-line,
  .rev-shadow,
  .rev-ribbon { stroke-dashoffset: 0; }

  .stat-card,
  .revenue-panel { transform: none; }
}
</style>