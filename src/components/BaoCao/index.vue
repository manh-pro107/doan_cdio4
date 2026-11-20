
<template>
  <div class="report-page">
    <!-- Background effects -->
    <div class="ambient ambient-purple"></div>
    <div class="ambient ambient-blue"></div>

    <!-- Header -->
    <header class="page-header">
      <div class="header-left">
        <div class="header-icon">
          <i class="bx bx-line-chart"></i>
        </div>

        <div>
          <div class="eyebrow">TECHSHOP ANALYTICS</div>
          <h1>Báo cáo doanh thu</h1>
          <p class="header-subtitle">
            Theo dõi hiệu suất kinh doanh của cửa hàng
          </p>
        </div>
      </div>

      <div class="header-actions">
        <!-- Lọc thời gian -->
        <div class="date-select">
          <i class="bx bx-calendar"></i>
          <select v-model="selectedTime">
            <option value="7">7 ngày gần đây</option>
            <option value="30">30 ngày gần đây</option>
            <option value="90">3 tháng gần đây</option>
            <option value="365">1 năm gần đây</option>
          </select>
        </div>

        <!-- Tỷ lệ lợi nhuận giả định -->
        <div class="date-select">
          <i class="bx bx-trending-up"></i>
          <select v-model="selectedMargin">
            <option value="10">Lợi nhuận 10%</option>
            <option value="15">Lợi nhuận 15%</option>
            <option value="20">Lợi nhuận 20%</option>
          </select>
        </div>

        <button class="btn-primary" @click="showModal = true">
          <i class="bx bx-export"></i>
          <span>Xuất báo cáo</span>
        </button>
      </div>
    </header>

    <!-- Loading -->
    <div v-if="loading" class="report-alert report-loading">
      <i class="bx bx-loader-alt bx-spin"></i>
      Đang tải dữ liệu báo cáo...
    </div>

    <!-- Error -->
    <div v-if="error" class="report-alert report-error">
      <i class="bx bx-error-circle"></i>

      <span>{{ error }}</span>

      <button @click="fetchReport">
        Thử lại
      </button>
    </div>

    <!-- KPI cards -->
    <section class="stats-grid">
      <!-- Revenue -->
      <article class="stat-card stat-purple">
        <div class="stat-top">
          <div class="stat-icon">
            <i class="bx bx-wallet-alt"></i>
          </div>

          <span class="stat-badge">
            <i class="bx bx-data"></i>
            Thực tế
          </span>
        </div>

        <p class="stat-label">Tổng doanh thu</p>

        <h2>{{ compactMoney(summary.revenue) }}</h2>

        <div class="stat-bottom">
          <span class="stat-description">
            Trong khoảng thời gian đã chọn
          </span>

          <i class="bx bx-bar-chart-alt-2"></i>
        </div>

        <div class="stat-glow"></div>
      </article>

      <!-- Orders -->
      <article class="stat-card stat-cyan">
        <div class="stat-top">
          <div class="stat-icon">
            <i class="bx bx-shopping-bag"></i>
          </div>

          <span class="stat-badge">
            <i class="bx bx-check-circle"></i>
            Hoàn thành
          </span>
        </div>

        <p class="stat-label">Tổng đơn hàng</p>

        <h2>{{ number(summary.orders) }}</h2>

        <div class="stat-bottom">
          <span class="stat-description">
            Số đơn đã giao thành công
          </span>

          <i class="bx bx-package"></i>
        </div>

        <div class="stat-glow"></div>
      </article>

      <!-- Estimated profit -->
      <article class="stat-card stat-pink">
        <div class="stat-top">
          <div class="stat-icon">
            <i class="bx bx-trending-up"></i>
          </div>

          <span class="stat-badge">
            Ước tính
          </span>
        </div>

        <p class="stat-label">Lợi nhuận ước tính</p>

        <h2>{{ compactMoney(summary.profit) }}</h2>

        <div class="stat-bottom">
          <span class="stat-description">
            Biên lợi nhuận {{ summary.margin }}%
          </span>

          <i class="bx bx-pie-chart-alt-2"></i>
        </div>

        <div class="stat-glow"></div>
      </article>

      <!-- Estimated cost -->
      <article class="stat-card stat-green">
        <div class="stat-top">
          <div class="stat-icon">
            <i class="bx bx-receipt"></i>
          </div>

          <span class="stat-badge">
            Ước tính
          </span>
        </div>

        <p class="stat-label">Giá vốn ước tính</p>

        <h2>{{ compactMoney(summary.estimatedCost) }}</h2>

        <div class="stat-bottom">
          <span class="stat-description">
            Doanh thu trừ lợi nhuận ước tính
          </span>

          <i class="bx bx-calculator"></i>
        </div>

        <div class="stat-glow"></div>
      </article>
    </section>

    <!-- Charts -->
    <section class="charts-grid">
      <!-- Revenue chart -->
      <article class="panel revenue-panel">
        <div class="panel-header">
          <div>
            <div class="panel-kicker">PERFORMANCE</div>

            <h2>Biến động doanh thu</h2>

            <p>
              Doanh thu theo thời gian đã chọn
            </p>
          </div>

          <div class="panel-icon purple-icon">
            <i class="bx bx-trending-up"></i>
          </div>
        </div>

        <div class="chart-summary">
          <div>
            <span class="chart-summary-label">
              Tổng doanh thu
            </span>

            <strong>
              {{ money(summary.revenue) }}
            </strong>
          </div>

          <span class="demo-tag">
            <i class="bx bx-data"></i>
            Dữ liệu thực tế
          </span>
        </div>

        <div class="line-chart-wrap">
          <Line
            ref="revenueChart"
            :data="lineData"
            :options="lineOptions"
          />
        </div>
      </article>

      <!-- Category chart -->
      <article class="panel category-panel">
        <div class="panel-header">
          <div>
            <div class="panel-kicker">PRODUCT MIX</div>

            <h2>Cơ cấu doanh thu</h2>

            <p>
              Tỷ trọng theo nhóm sản phẩm
            </p>
          </div>

          <div class="panel-icon cyan-icon">
            <i class="bx bx-doughnut-chart"></i>
          </div>
        </div>

        <div class="doughnut-wrap">
          <Doughnut
            ref="categoryChart"
            :data="pieData"
            :options="doughnutOptions"
          />

          <div class="doughnut-center">
            <span>NHÓM HÀNG</span>

            <strong>{{ categories.length }}</strong>

            <small>Danh mục</small>
          </div>
        </div>

        <div
          v-if="categories.length"
          class="category-legend"
        >
          <div
            v-for="item in categorySummary"
            :key="item.name"
            class="legend-row"
          >
            <div class="legend-name">
              <span
                class="legend-dot"
                :style="{ background: item.color }"
              ></span>

              <span>{{ item.name }}</span>
            </div>

            <strong>{{ item.percent }}%</strong>
          </div>
        </div>

        <div
          v-else
          class="empty-category"
        >
          Chưa có dữ liệu doanh thu theo danh mục.
        </div>
      </article>
    </section>

    <!-- Revenue table -->
    <section class="panel table-panel">
      <div class="panel-header table-panel-header">
        <div>
          <div class="panel-kicker">
            TRANSACTION OVERVIEW
          </div>

          <h2>Chi tiết doanh thu</h2>

          <p>
            Bảng thống kê theo từng khoảng thời gian
          </p>
        </div>

        <button class="btn-outline" @click="exportExcel">
          <i class="bx bx-download"></i>
          Xuất Excel
        </button>
      </div>

      <div class="table-responsive">
        <table class="revenue-table">
          <thead>
            <tr>
              <th>Thời gian</th>
              <th>Đơn hàng</th>
              <th>Doanh thu</th>
              <th>Lợi nhuận ước tính</th>
              <th>Giá trị trung bình</th>
              <th>Trạng thái</th>
            </tr>
          </thead>

          <tbody>
            <tr
              v-for="(item, index) in tableData"
              :key="item.label"
            >
              <td>
                <div class="period-cell">
                  <span class="period-index">
                    {{ String(index + 1).padStart(2, '0') }}
                  </span>

                  <strong>{{ item.label }}</strong>
                </div>
              </td>

              <td>
                <span class="order-number">
                  {{ number(item.orders) }}
                </span>
              </td>

              <td>
                <strong class="revenue-value">
                  {{ money(item.revenue) }}
                </strong>
              </td>

              <td>
                <span class="profit-value">
                  {{ money(item.profit) }}
                </span>
              </td>

              <td>
                {{ money(item.average) }}
              </td>

              <td>
                <span class="status-pill">
                  <span class="status-dot"></span>
                  Đã giao
                </span>
              </td>
            </tr>

            <tr v-if="!tableData.length">
              <td
                colspan="6"
                style="text-align: center; padding: 30px;"
              >
                Chưa có dữ liệu trong khoảng thời gian này.
              </td>
            </tr>
          </tbody>

          <tfoot>
            <tr>
              <td>
                <strong>TỔNG CỘNG</strong>
              </td>

              <td>
                <strong>
                  {{ number(summary.orders) }}
                </strong>
              </td>

              <td>
                <strong class="revenue-value">
                  {{ money(summary.revenue) }}
                </strong>
              </td>

              <td>
                <strong class="profit-value">
                  {{ money(summary.profit) }}
                </strong>
              </td>

              <td>
                <strong>
                  {{ money(summary.average) }}
                </strong>
              </td>

              <td>—</td>
            </tr>
          </tfoot>
        </table>
      </div>

      <div class="table-note">
        <i class="bx bx-info-circle"></i>

        Doanh thu lấy từ đơn hàng đã giao thành công.
        Lợi nhuận và giá vốn là số liệu ước tính theo tỷ lệ
        {{ selectedMargin }}% giả định.
      </div>
    </section>

    <!-- Workflow -->
    <section class="panel workflow-panel">
      <div class="panel-header">
        <div>
          <div class="panel-kicker">HOW IT WORKS</div>

          <h2>Quy trình báo cáo</h2>

          <p>
            Các bước tạo và xuất báo cáo doanh thu
          </p>
        </div>

        <div class="panel-icon pink-icon">
          <i class="bx bx-git-branch"></i>
        </div>
      </div>

      <div class="workflow-grid">
        <div class="workflow-step">
          <div class="workflow-number">01</div>

          <div class="workflow-icon purple-icon">
            <i class="bx bx-calendar-check"></i>
          </div>

          <h3>Chọn thời gian</h3>

          <p>
            Lựa chọn khoảng thời gian cần xem
          </p>
        </div>

        <div class="workflow-connector">
          <i class="bx bx-right-arrow-alt"></i>
        </div>

        <div class="workflow-step">
          <div class="workflow-number">02</div>

          <div class="workflow-icon cyan-icon">
            <i class="bx bx-data"></i>
          </div>

          <h3>Tổng hợp dữ liệu</h3>

          <p>
            Thống kê đơn hàng và doanh thu
          </p>
        </div>

        <div class="workflow-connector">
          <i class="bx bx-right-arrow-alt"></i>
        </div>

        <div class="workflow-step">
          <div class="workflow-number">03</div>

          <div class="workflow-icon pink-icon">
            <i class="bx bx-bar-chart-alt-2"></i>
          </div>

          <h3>Phân tích kết quả</h3>

          <p>
            Quan sát biểu đồ và bảng thống kê
          </p>
        </div>

        <div class="workflow-connector">
          <i class="bx bx-right-arrow-alt"></i>
        </div>

        <div class="workflow-step">
          <div class="workflow-number">04</div>

          <div class="workflow-icon green-icon">
            <i class="bx bx-cloud-download"></i>
          </div>

          <h3>Xuất báo cáo</h3>

          <p>
            Tải bảng dữ liệu hoặc lưu PDF, PNG
          </p>
        </div>
      </div>
    </section>

    <!-- Export modal -->
    <div
      v-if="showModal"
      class="modal-overlay"
      @click.self="showModal = false"
      @keydown.esc="showModal = false"
    >
      <div class="export-modal">
        <button
          class="modal-close"
          aria-label="Đóng"
          @click="showModal = false"
        >
          <i class="bx bx-x"></i>
        </button>

        <div class="modal-heading-icon">
          <i class="bx bx-export"></i>
        </div>

        <div class="panel-kicker">
          EXPORT CENTER
        </div>

        <h2>Xuất báo cáo</h2>

        <p class="modal-description">
          Chọn định dạng phù hợp để lưu báo cáo doanh thu.
        </p>

        <div class="export-grid">
          <!-- CSV -->
          <article class="export-card export-csv">
  <div class="export-card-icon">
    <i class="bx bx-spreadsheet"></i>
  </div>

  <h3>Excel (.xlsx)</h3>

  <p>
    Xuất báo cáo chuyên nghiệp với nhiều sheet,
    định dạng tiền và bảng thống kê.
  </p>

  <button @click="exportExcel">
    <i class="bx bx-download"></i>
    Tải Excel
  </button>
</article>

          <!-- PDF -->
          <article class="export-card export-pdf">
            <div class="export-card-icon">
              <i class="bx bxs-file-pdf"></i>
            </div>

            <h3>PDF</h3>

            <p>
              Mở cửa sổ in để lưu báo cáo thành PDF.
            </p>

            <button @click="exportPDF">
              <i class="bx bx-printer"></i>
              Lưu PDF
            </button>
          </article>

          <!-- PNG -->
          <article class="export-card export-png">
            <div class="export-card-icon">
              <i class="bx bx-image"></i>
            </div>

            <h3>Ảnh PNG</h3>

            <p>
              Lưu biểu đồ doanh thu thành hình ảnh.
            </p>

            <button @click="exportPNG">
              <i class="bx bx-download"></i>
              Xuất ảnh
            </button>
          </article>
        </div>

        <div class="modal-footnote">
          <i class="bx bx-shield-quarter"></i>

          Báo cáo sử dụng dữ liệu đơn hàng thực tế.
          Lợi nhuận là số liệu ước tính theo tỷ lệ đã chọn.
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
import ExcelJS from 'exceljs'
import { Line, Doughnut } from 'vue-chartjs'

import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  PointElement,
  ArcElement,
  CategoryScale,
  LinearScale,
  Filler
} from 'chart.js'

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  LineElement,
  PointElement,
  ArcElement,
  CategoryScale,
  LinearScale,
  Filler
)

const API = 'http://127.0.0.1:8000/api'

export default {
  name: 'BaoCaoDoanhThu',

  components: {
    Line,
    Doughnut
  },

  data() {
    return {
      showModal: false,

      // Bộ lọc thời gian
      selectedTime: '30',

      // Tỷ lệ lợi nhuận giả định
      selectedMargin: '15',

      // Trạng thái API
      loading: false,
      error: '',

      // Dùng để tránh request cũ ghi đè request mới
      requestId: 0,

      // KPI
      summary: {
        revenue: 0,
        orders: 0,
        profit: 0,
        estimatedCost: 0,
        average: 0,
        margin: 15
      },
      reportFilters: {
        startDate: '',
        endDate: ''
      },
      // Dữ liệu biểu đồ
      rawData: [],

      // Danh mục
      categories: [],

      // Bảng doanh thu
      tableData: [],

      // Màu biểu đồ danh mục
      colors: [
        '#9B8AFB',
        '#22D3EE',
        '#F472B6',
        '#FBBF24',
        '#34D399',
        '#60A5FA',
        '#FB7185',
        '#A3E635',
        '#C084FC',
        '#38BDF8'
      ]
    }
  },

  computed: {
    // =========================================
    // BIỂU ĐỒ DOANH THU
    // =========================================
    lineData() {
      return {
        labels: this.rawData.map(item => item.label),

        datasets: [
          {
            label: 'Doanh thu',

            data: this.rawData.map(item => item.revenue),

            borderColor: '#A78BFA',
            borderWidth: 3,

            tension: 0.4,
            fill: true,

            pointRadius: this.rawData.length > 30 ? 0 : 4,
            pointHoverRadius: 7,

            pointBackgroundColor: '#F8FAFC',
            pointBorderColor: '#A78BFA',
            pointBorderWidth: 3,

            backgroundColor: context => {
              const chart = context.chart

              const { ctx, chartArea } = chart

              if (!chartArea) {
                return 'rgba(139, 92, 246, 0.15)'
              }

              const gradient = ctx.createLinearGradient(
                0,
                chartArea.top,
                0,
                chartArea.bottom
              )

              gradient.addColorStop(
                0,
                'rgba(139, 92, 246, 0.34)'
              )

              gradient.addColorStop(
                1,
                'rgba(139, 92, 246, 0.01)'
              )

              return gradient
            }
          }
        ]
      }
    },

    // =========================================
    // BIỂU ĐỒ DANH MỤC
    // =========================================
    pieData() {
      return {
        labels: this.categories.map(item => item.name),

        datasets: [
          {
            data: this.categories.map(item => item.revenue),

            backgroundColor: this.categories.map(
              (_, index) => this.colors[index % this.colors.length]
            ),

            borderColor: '#11182B',
            borderWidth: 4,

            hoverOffset: 10,
            spacing: 3,
            borderRadius: 5
          }
        ]
      }
    },

    // =========================================
    // CHI TIẾT CHÚ GIẢI DANH MỤC
    // =========================================
    categorySummary() {
      return this.categories.map((item, index) => ({
        name: item.name,
        percent: Number(item.percent || 0),

        color: this.colors[index % this.colors.length]
      }))
    },

    // =========================================
    // CẤU HÌNH BIỂU ĐỒ ĐƯỜNG
    // =========================================
    lineOptions() {
      return {
        responsive: true,
        maintainAspectRatio: false,

        interaction: {
          intersect: false,
          mode: 'index'
        },

        plugins: {
          legend: {
            display: false
          },

          tooltip: {
            backgroundColor: '#171D32',
            titleColor: '#FFFFFF',
            bodyColor: '#DCE4F5',

            borderColor: 'rgba(167,139,250,0.35)',
            borderWidth: 1,

            padding: 12,
            displayColors: false,

            callbacks: {
              label: context => this.money(context.parsed.y)
            }
          }
        },

        scales: {
          x: {
            grid: {
              display: false
            },

            border: {
              display: false
            },

            ticks: {
              color: '#8793AD',
              maxTicksLimit: 8,
              maxRotation: 0
            }
          },

          y: {
            beginAtZero: true,

            border: {
              display: false
            },

            grid: {
              color: 'rgba(148,163,184,0.11)'
            },

            ticks: {
              color: '#8793AD',
              maxTicksLimit: 5,

              callback: value => this.compactMoney(value)
            }
          }
        }
      }
    },

    // =========================================
    // CẤU HÌNH BIỂU ĐỒ TRÒN
    // =========================================
    doughnutOptions() {
      return {
        responsive: true,
        maintainAspectRatio: false,

        cutout: '73%',

        plugins: {
          legend: {
            display: false
          },

          tooltip: {
            backgroundColor: '#171D32',
            titleColor: '#FFFFFF',
            bodyColor: '#DCE4F5',

            padding: 12,

            callbacks: {
              label: context => {
                return `${context.label}: ${this.money(context.parsed)}`
              }
            }
          }
        }
      }
    }
  },

  watch: {
    // Chọn khoảng thời gian mới
    selectedTime() {
      this.fetchReport()
    },

    // Chọn tỷ lệ lợi nhuận mới
    selectedMargin() {
      this.fetchReport()
    }
  },

  mounted() {
    // Tự động gọi API khi vào trang
    this.fetchReport()
  },

  methods: {
    // =========================================
    // LẤY DỮ LIỆU TỪ BACKEND
    // =========================================
    async fetchReport() {
      const currentRequest = ++this.requestId

      this.loading = true
      this.error = ''

      try {
        const token = localStorage.getItem('admin_token')

        if (!token) {
          throw new Error(
            'Không tìm thấy admin_token. Vui lòng đăng nhập lại tài khoản quản trị.'
          )
        }

        const response = await axios.get(
          `${API}/admin/bao-cao-doanh-thu`,
          {
            params: {
              days: this.selectedTime,
              margin: this.selectedMargin
            },

            headers: {
              Accept: 'application/json',
              Authorization: `Bearer ${token}`
            }
          }
        )

        // Bỏ qua kết quả của request cũ
        if (currentRequest !== this.requestId) {
          return
        }

        const result = response.data?.data
        this.reportFilters = {
        startDate: result?.filters?.startDate || '',
        endDate: result?.filters?.endDate || ''
}

        if (response.data?.status === false || !result) {
          throw new Error(
            response.data?.message ||
            'Backend không trả về dữ liệu báo cáo.'
          )
        }

        // Cập nhật KPI
        this.summary = {
          revenue: Number(result.summary?.revenue || 0),

          orders: Number(result.summary?.orders || 0),

          profit: Number(result.summary?.profit || 0),

          estimatedCost: Number(
            result.summary?.estimatedCost || 0
          ),

          average: Number(result.summary?.average || 0),

          margin: Number(
            result.summary?.margin || this.selectedMargin
          )
        }

        // Cập nhật biểu đồ doanh thu
        this.rawData = Array.isArray(result.chart)
          ? result.chart.map(item => ({
              label: item.label || '',

              revenue: Number(item.revenue || 0),

              orders: Number(item.orders || 0),

              profit: Number(item.profit || 0)
            }))
          : []

        // Cập nhật cơ cấu doanh thu danh mục
        this.categories = Array.isArray(result.categories)
          ? result.categories.map(item => ({
              name: item.name || 'Chưa phân loại',

              revenue: Number(item.revenue || 0),

              percent: Number(item.percent || 0)
            }))
          : []

        // Cập nhật bảng
        this.tableData = Array.isArray(result.table)
          ? result.table.map(item => ({
              label: item.label || '',

              orders: Number(item.orders || 0),

              revenue: Number(item.revenue || 0),

              profit: Number(item.profit || 0),

              average: Number(item.average || 0)
            }))
          : []
      } catch (error) {
        if (currentRequest !== this.requestId) {
          return
        }

        console.error(
          'Lỗi tải báo cáo doanh thu:',
          error
        )

        const status = error.response?.status

        if (status === 401 || status === 403) {
          this.error =
            'Không có quyền truy cập báo cáo hoặc phiên đăng nhập đã hết hạn.'
        } else if (status === 404) {
          this.error =
            'Không tìm thấy API báo cáo doanh thu. Kiểm tra lại route Backend.'
        } else if (error.response?.data?.message) {
          this.error = error.response.data.message
        } else {
          this.error =
            error.message ||
            'Không thể tải dữ liệu báo cáo doanh thu.'
        }

        // Xóa dữ liệu cũ khi API thất bại
        this.summary = {
          revenue: 0,
          orders: 0,
          profit: 0,
          estimatedCost: 0,
          average: 0,
          margin: Number(this.selectedMargin)
        }

        this.rawData = []
        this.categories = []
        this.tableData = []
      } finally {
        if (currentRequest === this.requestId) {
          this.loading = false
        }
      }
    },

    // =========================================
    // ĐỊNH DẠNG TIỀN
    // =========================================
    money(value) {
      return new Intl.NumberFormat('vi-VN', {
        style: 'currency',
        currency: 'VND',
        maximumFractionDigits: 0
      }).format(Number(value) || 0)
    },

    // =========================================
    // ĐỊNH DẠNG SỐ
    // =========================================
    number(value) {
      return new Intl.NumberFormat('vi-VN').format(
        Number(value) || 0
      )
    },

    // =========================================
    // ĐỊNH DẠNG TIỀN RÚT GỌN
    // =========================================
    compactMoney(value) {
      value = Number(value) || 0

      if (value >= 1000000000) {
        return `${(value / 1000000000).toFixed(1)} tỷ`
      }

      if (value >= 1000000) {
        return `${(value / 1000000).toFixed(1)} tr`
      }

      if (value >= 1000) {
        return `${(value / 1000).toFixed(0)} nghìn`
      }

      return this.number(value)
    },

    // =========================================
    // TẢI FILE
    // =========================================
    downloadFile(content, type, filename) {
      const blob = new Blob(
        [content],
        { type }
      )

      const url = URL.createObjectURL(blob)

      const link = document.createElement('a')

      link.href = url
      link.download = filename

      document.body.appendChild(link)

      link.click()

      link.remove()

      setTimeout(() => {
        URL.revokeObjectURL(url)
      }, 1000)
    },

    // =========================================
    // XUẤT CSV
    // =========================================
    
async exportExcel() {
  try {
    const workbook = new ExcelJS.Workbook()

    workbook.creator = 'TECHSHOP'
    workbook.lastModifiedBy = 'TECHSHOP Admin'
    workbook.created = new Date()
    workbook.modified = new Date()

    workbook.properties.title = 'Báo cáo doanh thu TECHSHOP'
    workbook.properties.subject = 'Thống kê doanh thu và lợi nhuận ước tính'
    workbook.properties.company = 'TECHSHOP'

    const margin = Number(this.selectedMargin) || 15

    const currencyFormat = '#,##0 "₫"'

    const navy = 'FF172554'
    const purple = 'FF7C3AED'
    const lightPurple = 'FFEDE9FE'
    const cyan = 'FF0891B2'
    const lightBlue = 'FFE0F2FE'
    const green = 'FF047857'
    const lightGreen = 'FFD1FAE5'
    const white = 'FFFFFFFF'
    const gray = 'FF64748B'
    const lightGray = 'FFF1F5F9'
    const borderColor = 'FFE2E8F0'

    const border = {
      top: { style: 'thin', color: { argb: borderColor } },
      left: { style: 'thin', color: { argb: borderColor } },
      bottom: { style: 'thin', color: { argb: borderColor } },
      right: { style: 'thin', color: { argb: borderColor } }
    }

    // Hàm tạo tiêu đề sheet
    const setTitle = (sheet, range, title) => {
      sheet.mergeCells(range)

      const cell = sheet.getCell(range.split(':')[0])

      cell.value = title
      cell.font = {
        name: 'Aptos Display',
        size: 19,
        bold: true,
        color: { argb: white }
      }

      cell.fill = {
        type: 'pattern',
        pattern: 'solid',
        fgColor: { argb: navy }
      }

      cell.alignment = {
        vertical: 'middle',
        horizontal: 'left'
      }

      sheet.getRow(cell.row).height = 38
    }

    // Hàm tạo dòng tiêu đề bảng
    const setHeader = (row) => {
      row.height = 30

      row.eachCell(cell => {
        cell.font = {
          name: 'Aptos',
          size: 11,
          bold: true,
          color: { argb: white }
        }

        cell.fill = {
          type: 'pattern',
          pattern: 'solid',
          fgColor: { argb: purple }
        }

        cell.alignment = {
          vertical: 'middle',
          horizontal: 'center',
          wrapText: true
        }

        cell.border = border
      })
    }

    // Hàm tạo kiểu cho dữ liệu
    const setDataRow = (row, index) => {
      row.height = 25

      row.eachCell(cell => {
        cell.font = {
          name: 'Aptos',
          size: 10,
          color: { argb: 'FF334155' }
        }

        cell.fill = {
          type: 'pattern',
          pattern: 'solid',
          fgColor: {
            argb: index % 2 === 0 ? white : lightGray
          }
        }

        cell.alignment = {
          vertical: 'middle'
        }

        cell.border = border
      })
    }

    const subtitle =
      `Thời gian: ${this.reportFilters.startDate || '—'} đến ${this.reportFilters.endDate || '—'}`
      + ` | Tỷ lệ lợi nhuận giả định: ${margin}%`

    // =================================================
    // SHEET 1: TỔNG QUAN
    // =================================================

    const overview = workbook.addWorksheet('Tong quan', {
      views: [{ showGridLines: false }]
    })

    overview.columns = [
      { width: 34 },
      { width: 25 },
      { width: 55 }
    ]

    setTitle(
      overview,
      'A1:C1',
      'BÁO CÁO DOANH THU TECHSHOP'
    )

    overview.mergeCells('A2:C2')
    overview.getCell('A2').value = subtitle

    overview.getCell('A2').font = {
      name: 'Aptos',
      size: 10,
      italic: true,
      color: { argb: gray }
    }

    overview.getCell('A2').alignment = {
      vertical: 'middle'
    }

    overview.getRow(2).height = 25

    overview.addRow([])

    const overviewHeader = overview.addRow([
      'CHỈ TIÊU',
      'GIÁ TRỊ',
      'GHI CHÚ'
    ])

    setHeader(overviewHeader)

    const kpiRows = [
      [
        'Tổng doanh thu',
        Number(this.summary.revenue || 0),
        'Doanh thu từ các đơn hàng đã giao thành công'
      ],
      [
        'Tổng đơn hàng hoàn thành',
        Number(this.summary.orders || 0),
        'Số đơn có trạng thái DaGiao'
      ],
      [
        'Lợi nhuận ước tính',
        Number(this.summary.profit || 0),
        `Tính theo tỷ lệ giả định ${margin}% trên doanh thu`
      ],
      [
        'Giá vốn ước tính',
        Number(this.summary.estimatedCost || 0),
        'Doanh thu trừ lợi nhuận ước tính'
      ],
      [
        'Giá trị trung bình mỗi đơn',
        Number(this.summary.average || 0),
        'Doanh thu chia cho số đơn hoàn thành'
      ]
    ]

    kpiRows.forEach((item, index) => {
      const row = overview.addRow(item)

      setDataRow(row, index)

      // index 1 = Tổng đơn hàng -> là số đếm, không phải tiền
      row.getCell(2).numFmt = index === 1 ? '#,##0' : currencyFormat
      row.getCell(2).alignment = {
        horizontal: 'right',
        vertical: 'middle'
      }

      if (index === 2) {
        row.getCell(2).font = {
          bold: true,
          color: { argb: green },
          size: 11
        }

        row.getCell(2).fill = {
          type: 'pattern',
          pattern: 'solid',
          fgColor: { argb: lightGreen }
        }
      }
    })

    // Phần ghi chú
    const noteRow = overview.lastRow.number + 2

    overview.mergeCells(`A${noteRow}:C${noteRow}`)

    overview.getCell(`A${noteRow}`).value =
      'LƯU Ý: Lợi nhuận và giá vốn là số liệu ước tính theo tỷ lệ giả định, không phải giá vốn nhập hàng thực tế.'

    overview.getCell(`A${noteRow}`).font = {
      name: 'Aptos',
      size: 10,
      italic: true,
      color: { argb: gray }
    }

    overview.getCell(`A${noteRow}`).alignment = {
      wrapText: true,
      vertical: 'middle'
    }

    overview.getRow(noteRow).height = 32

    // =================================================
    // SHEET 2: CHI TIẾT DOANH THU
    // =================================================

    const detail = workbook.addWorksheet('Chi tiet doanh thu', {
      views: [{
        state: 'frozen',
        ySplit: 4,
        topLeftCell: 'A5'
      }]
    })

    detail.columns = [
      { width: 24 },
      { width: 21 },
      { width: 25 },
      { width: 29 },
      { width: 25 },
      { width: 20 }
    ]

    setTitle(
      detail,
      'A1:F1',
      'CHI TIẾT DOANH THU'
    )

    detail.mergeCells('A2:F2')
    detail.getCell('A2').value = subtitle

    detail.getCell('A2').font = {
      name: 'Aptos',
      size: 10,
      italic: true,
      color: { argb: gray }
    }

    detail.getRow(2).height = 25

    detail.addRow([])

    const detailHeader = detail.addRow([
      'Thời gian',
      'Số đơn hoàn thành',
      'Doanh thu (VNĐ)',
      'Lợi nhuận ước tính (VNĐ)',
      'Giá trị trung bình (VNĐ)',
      'Trạng thái'
    ])

    setHeader(detailHeader)

    this.tableData.forEach((item, index) => {
      const row = detail.addRow([
        item.label,
        Number(item.orders || 0),
        Number(item.revenue || 0),
        Number(item.profit || 0),
        Number(item.average || 0),
        'Đã giao'
      ])

      setDataRow(row, index)

      ;[3, 4, 5].forEach(column => {
        row.getCell(column).numFmt = currencyFormat
        row.getCell(column).alignment = {
          horizontal: 'right',
          vertical: 'middle'
        }
      })

      row.getCell(4).font = {
        bold: true,
        color: { argb: green },
        size: 10
      }

      row.getCell(6).font = {
        bold: true,
        color: { argb: green },
        size: 10
      }
    })

    const totalRow = detail.addRow([
      'TỔNG CỘNG',
      Number(this.summary.orders || 0),
      Number(this.summary.revenue || 0),
      Number(this.summary.profit || 0),
      Number(this.summary.average || 0),
      ''
    ])

    totalRow.height = 31

    totalRow.eachCell(cell => {
      cell.font = {
        bold: true,
        color: { argb: white },
        size: 11
      }

      cell.fill = {
        type: 'pattern',
        pattern: 'solid',
        fgColor: { argb: navy }
      }

      cell.border = border
      cell.alignment = {
        vertical: 'middle'
      }
    })

    ;[3, 4, 5].forEach(column => {
      totalRow.getCell(column).numFmt = currencyFormat

      totalRow.getCell(column).alignment = {
        horizontal: 'right',
        vertical: 'middle'
      }
    })

    totalRow.getCell(4).font = {
      bold: true,
      color: { argb: 'FF86EFAC' },
      size: 11
    }

    const detailLastRow = detail.lastRow.number

    detail.autoFilter = {
      from: 'A4',
      to: `F${Math.max(4, detailLastRow - 1)}`
    }

    detail.pageSetup = {
      orientation: 'landscape',
      fitToPage: true,
      fitToWidth: 1,
      fitToHeight: 0
    }

    // =================================================
    // SHEET 3: CƠ CẤU DANH MỤC
    // =================================================

    const categorySheet = workbook.addWorksheet('Danh muc', {
      views: [{ showGridLines: false }]
    })

    categorySheet.columns = [
      { width: 32 },
      { width: 27 },
      { width: 20 },
      { width: 27 },
      { width: 27 }
    ]

    setTitle(
      categorySheet,
      'A1:E1',
      'PHÂN TÍCH DOANH THU THEO DANH MỤC'
    )

    categorySheet.mergeCells('A2:E2')
    categorySheet.getCell('A2').value = subtitle

    categorySheet.getCell('A2').font = {
      name: 'Aptos',
      size: 10,
      italic: true,
      color: { argb: gray }
    }

    categorySheet.getRow(2).height = 25

    categorySheet.addRow([])

    const categoryHeader = categorySheet.addRow([
      'Tên danh mục',
      'Doanh thu (VNĐ)',
      'Tỷ trọng',
      'Lợi nhuận ước tính (VNĐ)',
      'Giá vốn ước tính (VNĐ)'
    ])

    setHeader(categoryHeader)

    let totalCategoryRevenue = 0
    let totalCategoryProfit = 0
    let totalCategoryCost = 0

    this.categories.forEach((item, index) => {
  const revenue = Math.round(Number(item.revenue || 0))
  const profit = Math.round(revenue * margin / 100)
  const cost = revenue - profit

      totalCategoryRevenue += revenue
      totalCategoryProfit += profit
      totalCategoryCost += cost

      const row = categorySheet.addRow([
        item.name,
        revenue,
        Number(item.percent || 0) / 100,
        profit,
        cost
      ])

      setDataRow(row, index)

      row.getCell(2).numFmt = currencyFormat
      row.getCell(3).numFmt = '0.00%'
      row.getCell(4).numFmt = currencyFormat
      row.getCell(5).numFmt = currencyFormat

      ;[2, 4, 5].forEach(column => {
        row.getCell(column).alignment = {
          horizontal: 'right',
          vertical: 'middle'
        }
      })

      row.getCell(4).font = {
        color: { argb: green },
        bold: true,
        size: 10
      }
    })

    const categoryTotal = categorySheet.addRow([
      'TỔNG CỘNG',
      totalCategoryRevenue,
      this.categories.length
        ? this.categories.reduce(
            (sum, item) => sum + Number(item.percent || 0),
            0
          ) / 100
        : 0,
      totalCategoryProfit,
      totalCategoryCost
    ])

    categoryTotal.height = 31

    categoryTotal.eachCell(cell => {
      cell.font = {
        bold: true,
        color: { argb: white },
        size: 11
      }

      cell.fill = {
        type: 'pattern',
        pattern: 'solid',
        fgColor: { argb: navy }
      }

      cell.border = border
    })

    categoryTotal.getCell(2).numFmt = currencyFormat
    categoryTotal.getCell(3).numFmt = '0.00%'
    categoryTotal.getCell(4).numFmt = currencyFormat
    categoryTotal.getCell(5).numFmt = currencyFormat

    const categoryNoteRow = categorySheet.lastRow.number + 2

    categorySheet.mergeCells(
      `A${categoryNoteRow}:E${categoryNoteRow}`
    )

    categorySheet.getCell(`A${categoryNoteRow}`).value =
      'Lưu ý: Doanh thu theo danh mục được tính từ số lượng nhân giá bán của từng sản phẩm. Nếu đơn hàng có voucher, số liệu phân bổ danh mục có thể chênh lệch với tổng doanh thu sau giảm giá.'

    categorySheet.getCell(`A${categoryNoteRow}`).font = {
      name: 'Aptos',
      size: 10,
      italic: true,
      color: { argb: gray }
    }

    categorySheet.getCell(`A${categoryNoteRow}`).alignment = {
      wrapText: true,
      vertical: 'middle'
    }

    categorySheet.getRow(categoryNoteRow).height = 42

    categorySheet.autoFilter = {
      from: 'A4',
      to: `E${Math.max(4, categorySheet.lastRow.number - 2)}`
    }

    categorySheet.pageSetup = {
      orientation: 'landscape',
      fitToPage: true,
      fitToWidth: 1,
      fitToHeight: 0
    }

    // =================================================
    // TẢI FILE EXCEL
    // =================================================

    const buffer = await workbook.xlsx.writeBuffer()

    const blob = new Blob(
      [buffer],
      {
        type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
      }
    )

    const url = URL.createObjectURL(blob)

    const link = document.createElement('a')

    link.href = url

    link.download =
      `TECHSHOP_BaoCaoDoanhThu_${this.selectedTime}ngay.xlsx`

    document.body.appendChild(link)

    link.click()

    link.remove()

    setTimeout(() => {
      URL.revokeObjectURL(url)
    }, 1000)

    this.showModal = false

  } catch (error) {
    console.error('Lỗi xuất Excel:', error)

    window.alert(
      'Xuất Excel thất bại. Vui lòng kiểm tra Console để xem lỗi.'
    )
  }
},


    // =========================================
    // XUẤT PDF
    // =========================================
    exportPDF() {
      this.showModal = false

      document.body.classList.add('printing-report')

      const cleanup = () => {
        document.body.classList.remove('printing-report')
        window.removeEventListener('afterprint', cleanup)
      }
      window.addEventListener('afterprint', cleanup)

      // chờ modal đóng và chart resize xong rồi mới in
      setTimeout(() => window.print(), 300)
    },

    // =========================================
    // XUẤT BIỂU ĐỒ PNG
    // =========================================
    exportPNG() {
  const chart = this.$refs.revenueChart?.chart

  if (!chart) {
    window.alert('Biểu đồ chưa sẵn sàng. Vui lòng thử lại.')
    return
  }

  // Tăng độ nét tạm thời
  const oldDpr = chart.options.devicePixelRatio
  chart.options.devicePixelRatio = 3
  chart.resize()

  const src = chart.canvas
  const pad = 48
  const headerH = 150

  const out = document.createElement('canvas')
  out.width = src.width + pad * 2
  out.height = src.height + headerH + pad
  const ctx = out.getContext('2d')

  // Nền
  ctx.fillStyle = '#11182B'
  ctx.fillRect(0, 0, out.width, out.height)

  // Tiêu đề
  ctx.fillStyle = '#A78BFA'
  ctx.font = '700 22px Inter, Arial, sans-serif'
  ctx.fillText('TECHSHOP ANALYTICS', pad, 56)

  ctx.fillStyle = '#F8FAFF'
  ctx.font = '800 40px Inter, Arial, sans-serif'
  ctx.fillText('Biến động doanh thu', pad, 106)

  ctx.fillStyle = '#8D9AB5'
  ctx.font = '400 24px Inter, Arial, sans-serif'
  ctx.fillText(
    `${this.reportFilters.startDate || '—'} đến ${this.reportFilters.endDate || '—'}  |  Tổng: ${this.money(this.summary.revenue)}`,
    pad,
    140
  )

  // Biểu đồ
  ctx.drawImage(src, pad, headerH)

  // Trả lại độ nét cũ
  chart.options.devicePixelRatio = oldDpr
  chart.resize()

  const link = document.createElement('a')
  link.href = out.toDataURL('image/png')
  link.download = `TECHSHOP_BieuDoDoanhThu_${this.selectedTime}ngay.png`
  link.click()
}
  }
}
</script>
  <style>
  .report-page {
    --bg: #090D1B;
    --panel: #11182B;
    --panel-2: #151D33;
    --border: rgba(148, 163, 184, 0.14);
    --text: #F1F5FF;
    --muted: #8D9AB5;
    --purple: #A78BFA;
    --cyan: #22D3EE;
    --pink: #F472B6;
    --green: #34D399;
  
    position: relative;
    isolation: isolate;
    min-height: 100vh;
    overflow: hidden;
    padding: 32px;
    color: var(--text);
    background:
      radial-gradient(ellipse at 10% 0%, rgba(124, 58, 237, .13), transparent 35%),
      radial-gradient(ellipse at 95% 20%, rgba(6, 182, 212, .09), transparent 30%),
      var(--bg);
    font-family: Inter, "Segoe UI", Arial, sans-serif;
    position: relative;
    isolation: auto;
    overflow: visible;
  }
  
  .report-page *,
  .report-page *::before,
  .report-page *::after {
    box-sizing: border-box;
  }
  
  .report-page .ambient {
    position: absolute;
    z-index: -1;
    pointer-events: none;
    filter: blur(85px);
    border-radius: 50%;
    opacity: .14;
  }
  
  .report-page .ambient-purple {
    width: 270px;
    height: 270px;
    background: #8B5CF6;
    top: 230px;
    left: -170px;
  }
  
  .report-page .ambient-blue {
    width: 300px;
    height: 300px;
    background: #06B6D4;
    right: -190px;
    top: 650px;
  }
  
  .report-page button,
  .report-page select {
    font: inherit;
  }
  
  .report-page button {
    cursor: pointer;
  }
  
  /* Header */
  .report-page .page-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 24px;
    margin-bottom: 30px;
  }
  
  .report-page .header-left {
    display: flex;
    align-items: center;
    gap: 18px;
  }
  
  .report-page .header-icon {
    display: grid;
    place-items: center;
    width: 64px;
    height: 64px;
    flex-shrink: 0;
    color: white;
    font-size: 30px;
    border: 1px solid rgba(196, 181, 253, .4);
    border-radius: 20px;
    background: linear-gradient(145deg, #8B5CF6, #5B21B6);
    box-shadow:
      0 12px 30px rgba(124, 58, 237, .3),
      inset 0 1px 0 rgba(255, 255, 255, .3);
  }
  
  .report-page .eyebrow,
  .report-page .panel-kicker {
    margin-bottom: 7px;
    color: var(--purple);
    font-size: 10px;
    font-weight: 800;
    letter-spacing: 2px;
  }
  
  .report-page .page-header h1 {
    margin: 0;
    color: #F8FAFF;
    font-size: clamp(26px, 3vw, 36px);
    font-weight: 800;
    letter-spacing: -.8px;
  }
  
  .report-page .header-subtitle {
    margin: 8px 0 0;
    color: var(--muted);
    font-size: 14px;
  }
  
  .report-page .header-actions {
    display: flex;
    align-items: center;
    gap: 12px;
  }
  
  .report-page .date-select {
    display: flex;
    align-items: center;
    gap: 9px;
    min-height: 48px;
    padding: 0 14px;
    border: 1px solid var(--border);
    border-radius: 14px;
    background: rgba(17, 24, 43, .88);
    color: var(--cyan);
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, .025);
  }
  
  .report-page .date-select select {
    max-width: 180px;
    border: none;
    outline: none;
    color: #DDE5F7;
    background: transparent;
    font-size: 13px;
    cursor: pointer;
  }
  
  .report-page .date-select option {
    color: #111827;
    background: white;
  }
  
  .report-page .btn-primary {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: 9px;
    min-height: 48px;
    padding: 0 20px;
    color: white;
    font-size: 13px;
    font-weight: 700;
    border: 1px solid rgba(196, 181, 253, .45);
    border-radius: 14px;
    background: linear-gradient(135deg, #A78BFA, #7C3AED 55%, #6D28D9);
    box-shadow:
      0 10px 24px rgba(124, 58, 237, .24),
      inset 0 1px 0 rgba(255, 255, 255, .3);
    transition: transform .25s, box-shadow .25s;
  }
  
  .report-page .btn-primary:hover {
    transform: translateY(-3px);
    box-shadow: 0 16px 30px rgba(124, 58, 237, .35);
  }
  
  /* KPI */
  .report-page .stats-grid {
    display: grid;
    grid-template-columns: repeat(4, minmax(0, 1fr));
    gap: 18px;
    margin-bottom: 22px;
  }
  
  .report-page .stat-card {
    position: relative;
    min-width: 0;
    overflow: hidden;
    padding: 22px;
    border: 1px solid var(--border);
    border-radius: 22px;
    background: linear-gradient(145deg, rgba(24, 33, 57, .98), rgba(15, 21, 38, .98));
    box-shadow:
      0 15px 35px rgba(0, 0, 0, .17),
      inset 0 1px 0 rgba(255, 255, 255, .035);
    transition: transform .25s, border-color .25s;
  }
  
  .report-page .stat-card:hover {
    transform: translateY(-5px);
  }
  
  .report-page .stat-purple:hover { border-color: rgba(167, 139, 250, .5); }
  .report-page .stat-cyan:hover { border-color: rgba(34, 211, 238, .5); }
  .report-page .stat-pink:hover { border-color: rgba(244, 114, 182, .5); }
  .report-page .stat-green:hover { border-color: rgba(52, 211, 153, .5); }
  
  .report-page .stat-top,
  .report-page .stat-bottom {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10px;
  }
  
  .report-page .stat-icon {
    display: grid;
    place-items: center;
    width: 46px;
    height: 46px;
    border-radius: 15px;
    font-size: 24px;
    color: white;
    background: rgba(255, 255, 255, .06);
    border: 1px solid rgba(255, 255, 255, .07);
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, .06);
  }
  
  .report-page .stat-purple .stat-icon { color: #C4B5FD; background: rgba(139, 92, 246, .15); }
  .report-page .stat-cyan .stat-icon { color: #67E8F9; background: rgba(6, 182, 212, .13); }
  .report-page .stat-pink .stat-icon { color: #F9A8D4; background: rgba(244, 114, 182, .13); }
  .report-page .stat-green .stat-icon { color: #6EE7B7; background: rgba(52, 211, 153, .13); }
  
  .report-page .stat-badge {
    display: inline-flex;
    align-items: center;
    gap: 4px;
    padding: 6px 8px;
    color: #A5B4CC;
    font-size: 10px;
    border: 1px solid var(--border);
    border-radius: 9px;
    background: rgba(255, 255, 255, .025);
  }
  
  .report-page .stat-label {
    margin: 24px 0 8px;
    color: #9BA8C1;
    font-size: 13px;
  }
  
  .report-page .stat-card h2 {
    position: relative;
    z-index: 1;
    margin: 0 0 17px;
    color: #F8FAFF;
    font-size: clamp(20px, 2vw, 28px);
    font-weight: 800;
    letter-spacing: -.7px;
    overflow-wrap: anywhere;
  }
  
  .report-page .stat-bottom {
    position: relative;
    z-index: 1;
    padding-top: 14px;
    border-top: 1px solid rgba(148, 163, 184, .12);
  }
  
  .report-page .stat-description {
    color: #7E8CA7;
    font-size: 10px;
    line-height: 1.5;
  }
  
  .report-page .stat-bottom > i {
    font-size: 22px;
    opacity: .8;
  }
  
  .report-page .stat-purple .stat-bottom > i { color: var(--purple); }
  .report-page .stat-cyan .stat-bottom > i { color: var(--cyan); }
  .report-page .stat-pink .stat-bottom > i { color: var(--pink); }
  .report-page .stat-green .stat-bottom > i { color: var(--green); }
  
  .report-page .stat-glow {
    position: absolute;
    top: -65px;
    right: -60px;
    width: 145px;
    height: 145px;
    pointer-events: none;
    border-radius: 50%;
    filter: blur(55px);
    opacity: .22;
  }
  
  .report-page .stat-purple .stat-glow { background: #8B5CF6; }
  .report-page .stat-cyan .stat-glow { background: #06B6D4; }
  .report-page .stat-pink .stat-glow { background: #F472B6; }
  .report-page .stat-green .stat-glow { background: #34D399; }
  
  /* Panels */
  .report-page .charts-grid {
    display: grid;
    grid-template-columns: minmax(0, 1.65fr) minmax(300px, 1fr);
    gap: 20px;
    margin-bottom: 22px;
  }
  
  .report-page .panel {
    min-width: 0;
    padding: 25px;
    border: 1px solid var(--border);
    border-radius: 24px;
    background: linear-gradient(145deg, rgba(19, 27, 47, .97), rgba(14, 20, 36, .98));
    box-shadow:
      0 18px 40px rgba(0, 0, 0, .15),
      inset 0 1px 0 rgba(255, 255, 255, .035);
  }
  
  .report-page .panel-header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 18px;
    margin-bottom: 22px;
  }
  
  .report-page .panel-header h2 {
    margin: 0;
    color: #F1F5FF;
    font-size: 19px;
    font-weight: 750;
    letter-spacing: -.3px;
  }
  
  .report-page .panel-header p {
    margin: 8px 0 0;
    color: #8391AD;
    font-size: 12px;
  }
  
  .report-page .panel-icon,
  .report-page .workflow-icon {
    display: grid;
    place-items: center;
    flex-shrink: 0;
    width: 44px;
    height: 44px;
    border-radius: 14px;
    font-size: 23px;
    border: 1px solid rgba(255, 255, 255, .08);
  }
  
  .report-page .purple-icon { color: #C4B5FD; background: rgba(139, 92, 246, .14); }
  .report-page .cyan-icon { color: #67E8F9; background: rgba(6, 182, 212, .13); }
  .report-page .pink-icon { color: #F9A8D4; background: rgba(244, 114, 182, .13); }
  .report-page .green-icon { color: #6EE7B7; background: rgba(52, 211, 153, .13); }
  
  .report-page .chart-summary {
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    gap: 12px;
    margin-bottom: 15px;
  }
  
  .report-page .chart-summary-label {
    display: block;
    margin-bottom: 7px;
    color: #8391AD;
    font-size: 11px;
  }
  
  .report-page .chart-summary strong {
    color: #EDE9FE;
    font-size: 20px;
    font-weight: 800;
  }
  
  .report-page .demo-tag {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    padding: 7px 9px;
    color: #A5B4FC;
    font-size: 10px;
    white-space: nowrap;
    border-radius: 9px;
    background: rgba(139, 92, 246, .1);
    border: 1px solid rgba(139, 92, 246, .18);
  }
  
  .report-page .line-chart-wrap {
    position: relative;
    height: 300px;
    width: 100%;
  }
  
  .report-page .doughnut-wrap {
    position: relative;
    width: 100%;
    max-width: 245px;
    height: 220px;
    margin: 0 auto 18px;
  }
  
  .report-page .doughnut-center {
    position: absolute;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    pointer-events: none;
  }
  
  .report-page .doughnut-center span {
    color: #8491AD;
    font-size: 9px;
    font-weight: 800;
    letter-spacing: 1.5px;
  }
  
  .report-page .doughnut-center strong {
    margin: 5px 0 1px;
    color: #F8FAFF;
    font-size: 32px;
    font-weight: 800;
  }
  
  .report-page .doughnut-center small {
    color: #8D9AB5;
    font-size: 11px;
  }
  
  .report-page .category-legend {
    display: grid;
    gap: 13px;
  }
  
  .report-page .legend-row,
  .report-page .legend-name {
    display: flex;
    align-items: center;
  }
  
  .report-page .legend-row {
    justify-content: space-between;
    gap: 12px;
    color: #D8E0F1;
    font-size: 12px;
  }
  
  .report-page .legend-name {
    gap: 9px;
    min-width: 0;
  }
  
  .report-page .legend-name span:last-child {
    overflow-wrap: anywhere;
  }
  
  .report-page .legend-dot {
    width: 9px;
    height: 9px;
    flex-shrink: 0;
    border-radius: 3px;
    box-shadow: 0 0 12px currentColor;
  }
  
  .report-page .legend-row strong {
    color: #F1F5FF;
    font-size: 12px;
  }
  
  /* Table */
  .report-page .table-panel {
    margin-bottom: 22px;
  }
  
  .report-page .table-panel-header {
    align-items: center;
  }
  
  .report-page .btn-outline {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 10px 14px;
    color: #C4B5FD;
    font-size: 12px;
    font-weight: 700;
    white-space: nowrap;
    border: 1px solid rgba(167, 139, 250, .3);
    border-radius: 11px;
    background: rgba(139, 92, 246, .08);
    transition: background .2s, transform .2s;
  }
  
  .report-page .btn-outline:hover {
    transform: translateY(-2px);
    background: rgba(139, 92, 246, .17);
  }
  
  .report-page .table-responsive {
    width: 100%;
    overflow-x: auto;
  }
  
  .report-page .revenue-table {
    width: 100%;
    border-collapse: separate;
    border-spacing: 0;
    white-space: nowrap;
    font-size: 12px;
  }
  
  .report-page .revenue-table th {
    padding: 15px 14px;
    color: #8997B2;
    font-size: 10px;
    font-weight: 800;
    letter-spacing: .5px;
    text-align: left;
    background: rgba(148, 163, 184, .055);
    border-top: 1px solid rgba(148, 163, 184, .1);
    border-bottom: 1px solid rgba(148, 163, 184, .12);
  }
  
  .report-page .revenue-table th:first-child {
    border-radius: 10px 0 0 10px;
  }
  
  .report-page .revenue-table th:last-child {
    border-radius: 0 10px 10px 0;
  }
  
  .report-page .revenue-table td {
    padding: 16px 14px;
    color: #AFBBD0;
    border-bottom: 1px solid rgba(148, 163, 184, .09);
  }
  
  .report-page .revenue-table tbody tr {
    transition: background .2s;
  }
  
  .report-page .revenue-table tbody tr:hover {
    background: rgba(139, 92, 246, .055);
  }
  
  .report-page .period-cell {
    display: flex;
    align-items: center;
    gap: 10px;
  }
  
  .report-page .period-index {
    display: grid;
    place-items: center;
    width: 31px;
    height: 31px;
    color: #C4B5FD;
    font-size: 10px;
    border-radius: 9px;
    background: rgba(139, 92, 246, .12);
  }
  
  .report-page .period-cell strong {
    color: #E6EBF7;
    font-weight: 650;
  }
  
  .report-page .order-number {
    color: #67E8F9;
    font-weight: 750;
  }
  
  .report-page .revenue-value {
    color: #C4B5FD;
    font-weight: 750;
  }
  
  .report-page .profit-value {
    color: #6EE7B7;
    font-weight: 650;
  }
  
  .report-page .status-pill {
    display: inline-flex;
    align-items: center;
    gap: 7px;
    padding: 6px 9px;
    color: #A5B4FC;
    font-size: 10px;
    border-radius: 8px;
    background: rgba(139, 92, 246, .1);
  }
  
  .report-page .status-dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: #A78BFA;
    box-shadow: 0 0 9px rgba(167, 139, 250, .7);
  }
  
  .report-page .revenue-table tfoot td {
    color: #F1F5FF;
    font-weight: 700;
    background: rgba(148, 163, 184, .045);
    border-bottom: none;
  }
  
  .report-page .table-note {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-top: 17px;
    color: #7E8CA7;
    font-size: 11px;
  }
  
  .report-page .table-note i {
    color: var(--cyan);
    font-size: 16px;
  }
  
  /* Workflow */
  .report-page .workflow-panel {
    margin-bottom: 10px;
  }
  
  .report-page .workflow-grid {
    display: grid;
    grid-template-columns: 1fr auto 1fr auto 1fr auto 1fr;
    align-items: center;
    gap: 12px;
    padding-top: 10px;
  }
  
  .report-page .workflow-step {
    position: relative;
    min-width: 0;
    padding: 22px 13px;
    text-align: center;
    border: 1px solid rgba(148, 163, 184, .1);
    border-radius: 17px;
    background: rgba(255, 255, 255, .018);
    transition: transform .2s, border-color .2s, background .2s;
  }
  
  .report-page .workflow-step:hover {
    transform: translateY(-4px);
    border-color: rgba(167, 139, 250, .27);
    background: rgba(139, 92, 246, .045);
  }
  
  .report-page .workflow-number {
    position: absolute;
    top: 11px;
    left: 12px;
    color: #53617C;
    font-size: 10px;
    font-weight: 800;
  }
  
  .report-page .workflow-icon {
    margin: 0 auto 14px;
    width: 50px;
    height: 50px;
    font-size: 25px;
    border-radius: 16px;
  }
  
  .report-page .workflow-step h3 {
    margin: 0 0 8px;
    color: #E9EDF8;
    font-size: 12px;
    font-weight: 750;
  }
  
  .report-page .workflow-step p {
    margin: 0;
    color: #8391AD;
    font-size: 10px;
    line-height: 1.7;
  }
  
  .report-page .workflow-connector {
    color: #697796;
    font-size: 24px;
  }
  
  /* Modal */
  /* Modal - hiển thị phía trên header và sidebar */
.report-page .modal-overlay {
  position: fixed;
  inset: 0;
  z-index: 999999;

  display: flex;
  align-items: center;
  justify-content: center;

  padding: 20px;
  overflow-y: auto;
  background: rgba(3, 6, 16, .82);
  backdrop-filter: blur(12px);

  animation: reportFadeIn .2s ease;
}

.report-page .export-modal {
  position: relative;
  z-index: 1000000;

  width: min(820px, 100%);
  max-height: calc(100dvh - 40px);
  overflow-y: auto;

  margin: auto;
  padding: 34px;
  border: 1px solid rgba(167, 139, 250, .24);
  border-radius: 26px;

  background:
    radial-gradient(
      ellipse at 10% 0%,
      rgba(139, 92, 246, .13),
      transparent 45%
    ),
    #11182B;

  box-shadow:
    0 30px 100px rgba(0, 0, 0, .45),
    inset 0 1px 0 rgba(255, 255, 255, .04);

  animation: reportModalIn .25s ease;
}
  
  .report-page .modal-close {
    position: absolute;
    top: 18px;
    right: 18px;
    display: grid;
    place-items: center;
    width: 38px;
    height: 38px;
    color: #A8B4CC;
    font-size: 22px;
    border: 1px solid var(--border);
    border-radius: 12px;
    background: rgba(255, 255, 255, .035);
  }
  
  .report-page .modal-close:hover {
    color: white;
    background: rgba(244, 114, 182, .12);
  }
  
  .report-page .modal-heading-icon {
    display: grid;
    place-items: center;
    width: 48px;
    height: 48px;
    margin-bottom: 20px;
    color: #DDD6FE;
    font-size: 25px;
    border-radius: 15px;
    background: linear-gradient(145deg, #8B5CF6, #6D28D9);
    box-shadow: 0 10px 25px rgba(124, 58, 237, .25);
  }
  
  .report-page .export-modal > h2 {
    margin: 0;
    color: #F8FAFF;
    font-size: 27px;
    font-weight: 800;
  }
  
  .report-page .modal-description {
    margin: 9px 0 25px;
    color: #8D9AB5;
    font-size: 13px;
    line-height: 1.6;
  }
  
  .report-page .export-grid {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 14px;
  }
  
  .report-page .export-card {
    min-width: 0;
    padding: 20px 16px;
    border: 1px solid rgba(148, 163, 184, .14);
    border-radius: 19px;
    background: rgba(255, 255, 255, .025);
    transition: transform .2s, border-color .2s, background .2s;
  }
  
  .report-page .export-card:hover {
    transform: translateY(-4px);
    background: rgba(255, 255, 255, .045);
  }
  
  .report-page .export-csv:hover { border-color: rgba(52, 211, 153, .4); }
  .report-page .export-pdf:hover { border-color: rgba(244, 114, 182, .4); }
  .report-page .export-png:hover { border-color: rgba(34, 211, 238, .4); }
  
  .report-page .export-card-icon {
    display: grid;
    place-items: center;
    width: 53px;
    height: 53px;
    margin-bottom: 17px;
    color: white;
    font-size: 26px;
    border-radius: 16px;
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, .25);
  }
  
  .report-page .export-csv .export-card-icon {
    background: linear-gradient(145deg, #34D399, #047857);
  }
  
  .report-page .export-pdf .export-card-icon {
    background: linear-gradient(145deg, #FB7185, #BE123C);
  }
  
  .report-page .export-png .export-card-icon {
    background: linear-gradient(145deg, #22D3EE, #2563EB);
  }
  
  .report-page .export-card h3 {
    margin: 0 0 9px;
    color: #F1F5FF;
    font-size: 15px;
  }
  
  .report-page .export-card p {
    min-height: 53px;
    margin: 0 0 18px;
    color: #8D9AB5;
    font-size: 11px;
    line-height: 1.7;
  }
  
  .report-page .export-card button {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 7px;
    width: 100%;
    padding: 11px 8px;
    color: #F8FAFF;
    font-size: 11px;
    font-weight: 750;
    border: 1px solid rgba(167, 139, 250, .24);
    border-radius: 10px;
    background: rgba(139, 92, 246, .15);
    transition: background .2s;
  }
  
  .report-page .export-card button:hover {
    background: rgba(139, 92, 246, .3);
  }
  
  .report-page .modal-footnote {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-top: 23px;
    padding-top: 17px;
    color: #7E8CA7;
    font-size: 10px;
    border-top: 1px solid var(--border);
  }
  
  .report-page .modal-footnote i {
    color: var(--green);
    font-size: 16px;
  }
  
  @keyframes reportFadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
  }
  
  @keyframes reportModalIn {
    from { opacity: 0; transform: translateY(12px) scale(.98); }
    to { opacity: 1; transform: translateY(0) scale(1); }
  }
  
  /* Responsive */
  @media (max-width: 1200px) {
    .report-page {
      padding: 24px;
    }
  
    .report-page .stats-grid {
      grid-template-columns: repeat(2, minmax(0, 1fr));
    }
  
    .report-page .charts-grid {
      grid-template-columns: minmax(0, 1.3fr) minmax(280px, 1fr);
    }
  
    .report-page .workflow-grid {
      gap: 7px;
    }
  }
  
  @media (max-width: 900px) {
    .report-page .page-header {
      align-items: flex-start;
      flex-direction: column;
    }
  
    .report-page .header-actions {
      width: 100%;
      flex-wrap: wrap;
    }
  
    .report-page .charts-grid {
      grid-template-columns: 1fr;
    }
  
    .report-page .workflow-grid {
      grid-template-columns: repeat(2, minmax(0, 1fr));
      gap: 12px;
    }
  
    .report-page .workflow-connector {
      display: none;
    }
  }
  
  @media (max-width: 600px) {
    .report-page {
      padding: 16px;
    }
  
    .report-page .header-icon {
      width: 50px;
      height: 50px;
      border-radius: 15px;
      font-size: 24px;
    }
  
    .report-page .header-left {
      gap: 12px;
    }
  
    .report-page .header-subtitle {
      font-size: 12px;
    }
  
    .report-page .header-actions {
      align-items: stretch;
      flex-direction: column;
    }
  
    .report-page .date-select {
      min-height: 46px;
    }
  
    .report-page .date-select select {
      max-width: none;
      width: 100%;
    }
  
    .report-page .btn-primary {
      width: 100%;
    }
  
    .report-page .stats-grid {
      grid-template-columns: 1fr;
      gap: 12px;
    }
  
    .report-page .stat-card {
      padding: 19px;
    }
  
    .report-page .panel {
      padding: 18px;
      border-radius: 19px;
    }
  
    .report-page .panel-header h2 {
      font-size: 17px;
    }
  
    .report-page .line-chart-wrap {
      height: 240px;
    }
  
    .report-page .chart-summary {
      align-items: flex-start;
      flex-direction: column;
    }
  
    .report-page .workflow-grid {
      grid-template-columns: 1fr;
    }
  
    .report-page .workflow-step {
      padding: 23px 15px;
    }
  
    .report-page .export-modal {
      padding: 25px 18px;
    }
  
    .report-page .export-grid {
      grid-template-columns: 1fr;
    }
  
    .report-page .export-card {
      display: grid;
      grid-template-columns: 54px 1fr;
      column-gap: 13px;
      align-items: center;
    }
  
    .report-page .export-card-icon {
      grid-row: span 2;
      margin: 0;
    }
  
    .report-page .export-card h3 {
      margin: 0 0 5px;
    }
  
    .report-page .export-card p {
      min-height: 0;
      margin: 0 0 12px;
    }
  
    .report-page .export-card button {
      grid-column: 1 / -1;
    }
  
    .report-page .table-panel-header {
      align-items: flex-start;
    }
  }
  
  /* PDF printing */
  @media print {
    @page {
  size: A4 landscape;
  margin: 10mm;
}

@media print {
  /* Ẩn toàn bộ layout admin, chỉ giữ trang báo cáo */
  body.printing-report * {
    visibility: hidden !important;
  }

  body.printing-report .report-page,
  body.printing-report .report-page * {
    visibility: visible !important;
  }

  body.printing-report .report-page {
    position: absolute !important;
    left: 0;
    top: 0;
    width: 100% !important;
    min-height: 0 !important;
  }

  /* Bỏ giới hạn chiều cao/cuộn của layout để không bị cắt hoặc tràn trang */
  body.printing-report,
  body.printing-report html,
  body.printing-report #app {
    height: auto !important;
    overflow: visible !important;
  }

  /* Giữ màu của biểu đồ và badge khi in */
  .report-page * {
    -webkit-print-color-adjust: exact;
    print-color-adjust: exact;
  }

  .report-page .line-chart-wrap { height: 260px; }
  .report-page .panel,
  .report-page .stat-card { break-inside: avoid; }

  /* Chữ nền sáng phải tối màu */
  .report-page .stat-label,
  .report-page .stat-description,
  .report-page .panel-header p,
  .report-page .header-subtitle,
  .report-page .legend-row,
  .report-page .legend-row strong,
  .report-page .chart-summary strong,
  .report-page .doughnut-center strong,
  .report-page .revenue-table th {
    color: #111827 !important;
  }
}

    body {
      background: white !important;
    }
  
    .report-page {
      padding: 12px;
      color: #111827 !important;
      background: white !important;
    }
  
    .report-page .ambient,
    .report-page .header-actions,
    .report-page .btn-outline,
    .report-page .workflow-panel,
    .report-page .modal-overlay {
      display: none !important;
    }
  
    .report-page .panel,
    .report-page .stat-card {
      color: #111827 !important;
      background: white !important;
      box-shadow: none !important;
      break-inside: avoid;
      border: 1px solid #D1D5DB !important;
    }
  
    .report-page .page-header h1,
    .report-page .panel-header h2,
    .report-page .stat-card h2,
    .report-page .revenue-table td,
    .report-page .revenue-table tfoot td {
      color: #111827 !important;
    }
  
    .report-page .charts-grid {
      grid-template-columns: 1.5fr 1fr;
    }
  
    .report-page .stats-grid {
      grid-template-columns: repeat(2, 1fr);
    }
  
    .report-page .stat-glow {
      display: none;
    }
  }











  /* Loading và thông báo lỗi API */
.report-page .report-alert {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
  padding: 14px 17px;
  border-radius: 13px;
  font-size: 13px;
}

.report-page .report-loading {
  color: #67E8F9;
  background: rgba(6, 182, 212, .09);
  border: 1px solid rgba(6, 182, 212, .2);
}

.report-page .report-loading i {
  font-size: 19px;
}

.report-page .report-error {
  color: #FDA4AF;
  background: rgba(244, 63, 94, .09);
  border: 1px solid rgba(244, 63, 94, .25);
}

.report-page .report-error button {
  margin-left: auto;
  padding: 8px 13px;
  color: white;
  border: 1px solid rgba(244, 114, 182, .3);
  border-radius: 9px;
  background: rgba(244, 63, 94, .18);
}

.report-page .empty-category {
  padding: 20px 5px;
  color: #8391AD;
  font-size: 12px;
  text-align: center;
}
  </style>
  