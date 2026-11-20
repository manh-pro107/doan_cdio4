
<template>
  <div class="category-page">
    <!-- Tiêu đề -->
    <div class="page-heading">
      <div>
        <div class="breadcrumb">Quản lý / Danh mục</div>
        <h1>Quản lý danh mục</h1>
        <p>Quản lý và sắp xếp các danh mục sản phẩm của cửa hàng.</p>
      </div>

      <button class="btn-primary" @click="openAddModal">
        <span class="plus">+</span>
        Thêm danh mục
      </button>
    </div>

    <!-- Thống kê -->
    <div class="stats-grid">
      <div class="stat-card">
        <div class="stat-icon icon-blue">▤</div>
        <div>
          <div class="stat-label">Tổng danh mục</div>
          <div class="stat-number">{{ categories.length }}</div>
          <div class="stat-note">Danh mục hiện có</div>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon icon-green">✓</div>
        <div>
          <div class="stat-label">Đang hoạt động</div>
          <div class="stat-number">{{ activeCount }}</div>
          <div class="stat-note">Có thể sử dụng</div>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon icon-orange">Ⅱ</div>
        <div>
          <div class="stat-label">Ngừng hoạt động</div>
          <div class="stat-number">{{ inactiveCount }}</div>
          <div class="stat-note">Đang tạm ngưng</div>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon icon-purple">▦</div>
        <div>
          <div class="stat-label">Sản phẩm</div>
          <div class="stat-number">{{ totalProducts }}</div>
          <div class="stat-note">Trong các danh mục mẫu</div>
        </div>
      </div>
    </div>

    <!-- Khu vực danh sách -->
    <section class="content-card">
      <div class="list-heading">
        <div>
          <h2>Danh sách danh mục</h2>
          <p>Tra cứu và quản lý danh mục sản phẩm</p>
        </div>

        <span class="result-count">
          {{ filteredCategories.length }} kết quả
        </span>
      </div>

      <!-- Bộ lọc -->
      <div class="filter-bar">
        <div class="search-box">
          <span class="search-icon">⌕</span>
          <input
            v-model.trim="keyword"
            type="text"
            placeholder="Tìm tên hoặc mã danh mục..."
          />
          <button
            v-if="keyword"
            class="clear-search"
            @click="keyword = ''"
            title="Xóa tìm kiếm"
          >
            ×
          </button>
        </div>

        <div class="filter-tabs">
          <button
            v-for="item in filterOptions"
            :key="item.value"
            :class="{ active: statusFilter === item.value }"
            @click="statusFilter = item.value"
          >
            {{ item.label }}
          </button>
        </div>
      </div>

      <!-- Bảng dữ liệu -->
      <div class="table-wrapper">
        <table>
          <thead>
            <tr>
              <th class="col-category">TÊN DANH MỤC</th>
              <th>MÃ DANH MỤC</th>
              <th>MÔ TẢ</th>
              <th class="text-center">SẢN PHẨM</th>
              <th>TRẠNG THÁI</th>
              <th>NGÀY TẠO</th>
              <th class="text-center">THAO TÁC</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="item in filteredCategories" :key="item.id">
              <td>
                <div class="category-name">
                  <div class="category-avatar" :class="item.color">
                    {{ item.name.charAt(0) }}
                  </div>
                  <div>
                    <div class="name-text">{{ item.name }}</div>
                    <div class="name-sub">Danh mục sản phẩm</div>
                  </div>
                </div>
              </td>

              <td>
                <span class="code-badge">{{ item.code }}</span>
              </td>

              <td>
                <span class="description-text">
                  {{ item.description || "Chưa có mô tả" }}
                </span>
              </td>

              <td class="text-center">
                <span class="product-count">{{ item.products }}</span>
              </td>

              <td>
                <span
                  class="status-badge"
                  :class="
                    item.status === 'active'
                      ? 'status-active'
                      : 'status-inactive'
                  "
                >
                  <span class="status-dot"></span>
                  {{ item.status === "active" ? "Hoạt động" : "Tạm ngưng" }}
                </span>
              </td>

              <td class="date-text">{{ item.createdAt }}</td>

              <td>
                <div class="action-group">
                  <button
                    v-if="item.status === 'active'"
                    class="action-btn delete-btn"
                    title="Ẩn danh mục"
                    @click="deleteCategory(item)"
                  >
                    ×
                  </button>
                  

                  <button
                    v-else
                    class="action-btn edit-btn"
                    title="Hiện lại danh mục"
                    @click="restoreCategory(item)"
                  >
                    ↻
                  </button>


                  <button
                    class="action-btn edit-btn"
                    title="Chỉnh sửa"
                    @click="openEditModal(item)"
                  >
                    ✎
                  </button>
                </div>
              </td>
            </tr>

            <tr v-if="filteredCategories.length === 0">
              <td colspan="7">
                <div class="empty-state">
                  <div class="empty-icon">⌕</div>
                  <h3>Không tìm thấy danh mục</h3>
                  <p>Thử thay đổi từ khóa hoặc bộ lọc của bạn.</p>
                  <button class="btn-secondary" @click="resetFilters">
                    Xóa bộ lọc
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="table-footer">
        <span>Đang hiển thị {{ filteredCategories.length }} danh mục</span>
        <span>Dữ liệu từ hệ thống</span>
      </div>
    </section>

    <!-- Form thêm / sửa -->
    <div v-if="showModal" class="modal-overlay" @click.self="closeModal">
      <section class="modal-card">
        <div class="modal-heading">
          <div>
            <h2>
              {{ editingId ? "Chỉnh sửa danh mục" : "Thêm danh mục mới" }}
            </h2>
            <p>
              {{
                editingId
                  ? "Cập nhật thông tin danh mục sản phẩm."
                  : "Điền thông tin để tạo danh mục sản phẩm."
              }}
            </p>
          </div>

          <button class="modal-close" @click="closeModal">×</button>
        </div>

        <form @submit.prevent="saveCategory">
          <div class="form-group">
            <label for="category-name"> Tên danh mục <span>*</span> </label>
            <input
              id="category-name"
              v-model.trim="form.name"
              type="text"
              placeholder="Ví dụ: Laptop"
              required
              maxlength="100"
            />
          </div>

          <div class="form-group">
            <label for="category-code"> Mã danh mục <span>*</span> </label>
            <input
              id="category-code"
              v-model.trim="form.code"
              type="text"
              placeholder="Mã sẽ do hệ thống quản lý"
              readonly
            />
          </div>

          <div class="form-group">
            <label for="category-description">Mô tả</label>
            <textarea
              id="category-description"
              v-model.trim="form.description"
              rows="3"
              placeholder="Nhập mô tả ngắn về danh mục..."
              maxlength="255"
            ></textarea>
          </div>

          

          <div class="modal-actions">
            <button type="button" class="btn-secondary" @click="closeModal">
              Hủy
            </button>
            <button type="submit" class="btn-primary">
              {{ editingId ? "Lưu thay đổi" : "Tạo danh mục" }}
            </button>
          </div>
        </form>
      </section>
    </div>

    <!-- Thông báo thao tác -->
    <div v-if="toast" class="toast-message">
      <span>✓</span>
      {{ toast }}
    </div>
  </div>
</template>



<script>
import axios from "axios";

const API = "http://127.0.0.1:8000/api";

export default {
  name: "DanhMuc",

  data() {
    return {
      categories: [],
      products: [],
      keyword: "",
      statusFilter: "all",
      showModal: false,
      editingId: null,
      toast: "",
      toastTimer: null,

      filterOptions: [
        { label: "Tất cả", value: "all" },
        { label: "Hoạt động", value: "active" },
        { label: "Tạm ngưng", value: "inactive" },
      ],

      form: {
        name: "",
        code: "",
        description: "",
        status: "active",
      },
    };
  },

  computed: {
    activeCount() {
      return this.categories.filter((item) => item.status === "active").length;
    },

    inactiveCount() {
      return this.categories.filter((item) => item.status === "inactive")
        .length;
    },

    totalProducts() {
      return this.products.length;
    },

    filteredCategories() {
      const keyword = this.keyword.toLowerCase();

      return this.categories.filter((item) => {
        const matchesKeyword =
          item.name.toLowerCase().includes(keyword) ||
          String(item.id).includes(keyword);

        const matchesStatus =
          this.statusFilter === "all" || item.status === this.statusFilter;

        return matchesKeyword && matchesStatus;
      });
    },
  },

  mounted() {
    this.layDanhMuc();
    this.laySanPham();
  },

  beforeUnmount() {
    clearTimeout(this.toastTimer);
  },

  methods: {
    getConfig() {
      const token = localStorage.getItem("admin_token");

      return {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      };
    },

    // Chuyển trạng thái từ database sang giao diện
    layTrangThai(value) {
      return value === "Hien" ? "active" : "inactive";
    },

    // Lấy danh sách danh mục
    async layDanhMuc() {
      try {
        const res = await axios.get(
          `${API}/danh-muc/get-data`,
          this.getConfig()
        );

        if (!res.data.status) {
          throw new Error(res.data.message || "Không lấy được danh mục.");
        }

        const data = Array.isArray(res.data.data) ? res.data.data : [];

        this.categories = data.map((item) => {
          const id = item.MaDM ?? item.MaDanhMuc;
          const name = item.TenDM ?? item.TenDanhMuc ?? "";

          return {
            id,
            name,
            code: `DM${String(id).padStart(3, "0")}`,
            description: item.MoTa || "",
            status: this.layTrangThai(item.TrangThaiDM),
            products: this.products.filter(
              (product) =>
                String(product.MaDM ?? product.MaDanhMuc) === String(id)
            ).length,
            createdAt: item.created_at
              ? new Date(item.created_at).toLocaleDateString("vi-VN")
              : "—",
            color: "avatar-blue",
          };
        });
      } catch (error) {
        console.error("Lỗi lấy danh mục:", error.response?.data || error);

        this.showToast(
          error.response?.data?.message ||
            error.message ||
            "Không thể tải danh mục."
        );
      }
    },

    // Lấy danh sách sản phẩm để đếm số sản phẩm mỗi danh mục
    async laySanPham() {
      try {
        const res = await axios.get(
          `${API}/san-pham/get-data`,
          this.getConfig()
        );

        if (res.data.status && Array.isArray(res.data.data)) {
          this.products = res.data.data;

          this.categories.forEach((category) => {
            category.products = this.products.filter(
              (product) =>
                String(product.MaDM ?? product.MaDanhMuc) ===
                String(category.id)
            ).length;
          });
        }
      } catch (error) {
        console.error("Lỗi lấy sản phẩm:", error.response?.data || error);
      }
    },

    // Mở form thêm danh mục
    openAddModal() {
      this.editingId = null;

      this.form = {
        name: "",
        code: "",
        description: "",
        status: "active",
      };

      this.showModal = true;
    },

    // Mở form sửa danh mục
    openEditModal(item) {
      this.editingId = item.id;

      this.form = {
        name: item.name,
        code: item.code,
        description: item.description,
        status: item.status,
      };

      this.showModal = true;
    },

    closeModal() {
      this.showModal = false;
    },

    // Thêm hoặc cập nhật danh mục
    async saveCategory() {
      const name = this.form.name.trim();

      if (!name) {
        this.showToast("Vui lòng nhập tên danh mục.");
        return;
      }

      const payload = {
        TenDM: name,
        MoTa: this.form.description.trim() || null,
      };

      try {
        let res;

        if (this.editingId !== null) {
          res = await axios.put(
            `${API}/danh-muc/update`,
            {
              ...payload,
              id: this.editingId,
            },
            this.getConfig()
          );
        } else {
          res = await axios.post(
            `${API}/danh-muc/add-data`,
            payload,
            this.getConfig()
          );
        }

        if (!res.data.status) {
          throw new Error(res.data.message || "Thao tác thất bại.");
        }

        const isEditing = this.editingId !== null;

        this.closeModal();

        this.showToast(
          isEditing
            ? "Cập nhật danh mục thành công."
            : "Thêm danh mục thành công."
        );

        await this.layDanhMuc();
        await this.laySanPham();
      } catch (error) {
        console.error("Lỗi lưu danh mục:", error.response?.data || error);

        this.showToast(
          error.response?.data?.message ||
            error.message ||
            "Không thể lưu danh mục."
        );
      }
    },

    // Ẩn danh mục, không xóa dữ liệu
    async deleteCategory(item) {
      if (!window.confirm(`Bạn có chắc muốn ẩn danh mục "${item.name}"?`)) {
        return;
      }

      try {
        const res = await axios.delete(`${API}/danh-muc/delete`, {
          ...this.getConfig(),
          data: {
            id: item.id,
          },
        });

        if (!res.data.status) {
          throw new Error(res.data.message || "Ẩn danh mục thất bại.");
        }

        this.showToast("Ẩn danh mục thành công.");

        await this.layDanhMuc();
        await this.laySanPham();
      } catch (error) {
        console.error("Lỗi ẩn danh mục:", error.response?.data || error);

        this.showToast(
          error.response?.data?.message ||
            error.message ||
            "Không thể ẩn danh mục."
        );
      }
    },

    // Hiện lại danh mục đã ẩn
    async restoreCategory(item) {
      try {
        const res = await axios.put(
          `${API}/danh-muc/restore`,
          {
            id: item.id,
          },
          this.getConfig()
        );

        if (!res.data.status) {
          throw new Error(res.data.message || "Hiện lại danh mục thất bại.");
        }

        this.showToast("Hiện lại danh mục thành công.");

        await this.layDanhMuc();
        await this.laySanPham();
      } catch (error) {
        console.error("Lỗi hiện lại danh mục:", error.response?.data || error);

        this.showToast(
          error.response?.data?.message ||
            error.message ||
            "Không thể hiện lại danh mục."
        );
      }
    },

    // Xóa từ khóa tìm kiếm và bộ lọc
    resetFilters() {
      this.keyword = "";
      this.statusFilter = "all";
    },

    // Hiển thị thông báo
    showToast(message) {
      this.toast = message;

      clearTimeout(this.toastTimer);

      this.toastTimer = setTimeout(() => {
        this.toast = "";
      }, 3000);
    },
  },
};
</script>

<style scoped>
/* =========================================================
   BẢNG MÀU & BIẾN DÙNG CHUNG
   ========================================================= */
.category-page {
  --primary: #4f46e5;
  --primary-2: #2563eb;
  --accent: #06b6d4;
  --navy: #0f172a;
  --text: #1e293b;
  --muted: #64748b;
  --line: #e2e8f0;
  --soft: #f1f5f9;
  --grad-primary: linear-gradient(135deg, #4f46e5 0%, #2563eb 55%, #06b6d4 100%);
  --grad-danger: linear-gradient(135deg, #f43f5e 0%, #e11d48 100%);
  --grad-success: linear-gradient(135deg, #34d399 0%, #059669 100%);
  --grad-warm: linear-gradient(135deg, #fbbf24 0%, #f97316 100%);
  --shadow-sm: 0 2px 8px rgba(15, 23, 42, 0.06);
  --ease: cubic-bezier(0.22, 1, 0.36, 1);

  width: 100%;
  min-width: 0;
  color: var(--text);
  font-family: "Roboto", "Inter", Arial, sans-serif;
}

.category-page * {
  box-sizing: border-box;
}

/* =========================================================
   KEYFRAMES
   ========================================================= */
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(16px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes rowIn {
  from { opacity: 0; transform: translateX(-10px); }
  to { opacity: 1; transform: translateX(0); }
}

@keyframes pulseDot {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.35); }
}

@keyframes shine {
  from { transform: translateX(-120%) skewX(-20deg); }
  to { transform: translateX(260%) skewX(-20deg); }
}

@keyframes overlayIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes modalIn {
  from { opacity: 0; transform: translateY(28px) scale(0.95); }
  to { opacity: 1; transform: translateY(0) scale(1); }
}

@keyframes toastIn {
  from { opacity: 0; transform: translateX(30px) scale(0.95); }
  to { opacity: 1; transform: translateX(0) scale(1); }
}

.page-heading { animation: fadeUp 0.5s var(--ease) both; }

.stat-card { animation: fadeUp 0.55s var(--ease) both; }
.stat-card:nth-child(1) { animation-delay: 0.05s; }
.stat-card:nth-child(2) { animation-delay: 0.12s; }
.stat-card:nth-child(3) { animation-delay: 0.19s; }
.stat-card:nth-child(4) { animation-delay: 0.26s; }

.content-card { animation: fadeUp 0.6s var(--ease) 0.3s both; }

tbody tr { animation: rowIn 0.4s var(--ease) both; }
tbody tr:nth-child(1) { animation-delay: 0.04s; }
tbody tr:nth-child(2) { animation-delay: 0.08s; }
tbody tr:nth-child(3) { animation-delay: 0.12s; }
tbody tr:nth-child(4) { animation-delay: 0.16s; }
tbody tr:nth-child(5) { animation-delay: 0.2s; }
tbody tr:nth-child(6) { animation-delay: 0.24s; }
tbody tr:nth-child(7) { animation-delay: 0.28s; }
tbody tr:nth-child(8) { animation-delay: 0.32s; }

/* =========================================================
   TIÊU ĐỀ
   ========================================================= */
.page-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 26px;
}

.breadcrumb {
  display: inline-flex;
  margin-bottom: 10px;
  padding: 4px 12px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 12px;
  font-weight: 700;
}

.page-heading h1 {
  margin: 0;
  font-size: 29px;
  font-weight: 800;
  letter-spacing: -0.4px;
  background: linear-gradient(90deg, #0f172a 0%, #4f46e5 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.page-heading p {
  margin: 8px 0 0;
  color: var(--muted);
  font-size: 14px;
}

/* =========================================================
   NÚT
   ========================================================= */
.btn-primary,
.btn-secondary {
  position: relative;
  overflow: hidden;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 9px;
  min-height: 42px;
  padding: 0 18px;
  border: 0;
  border-radius: 11px;
  font-size: 13px;
  font-weight: 700;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease),
    background 0.25s, border-color 0.25s;
}

.btn-primary {
  color: #fff;
  background: var(--grad-primary);
  background-size: 140% 140%;
  background-position: 0% 50%;
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.32);
}

.btn-primary::after {
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

.btn-primary:hover {
  transform: translateY(-2px);
  background-position: 100% 50%;
  box-shadow: 0 12px 26px rgba(79, 70, 229, 0.42);
}

.btn-primary:hover::after {
  animation: shine 0.8s var(--ease);
}

.btn-primary:active {
  transform: scale(0.97);
}

.btn-secondary {
  border: 1.5px solid var(--line);
  color: #475569;
  background: #fff;
}

.btn-secondary:hover {
  color: var(--primary);
  border-color: #c7d2fe;
  background: #eef2ff;
  transform: translateY(-2px);
}

.plus {
  display: inline-block;
  font-size: 21px;
  font-weight: 400;
  line-height: 1;
  transition: transform 0.4s var(--ease);
}

.btn-primary:hover .plus {
  transform: rotate(90deg);
}

/* =========================================================
   THẺ THỐNG KÊ
   ========================================================= */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 18px;
  margin-bottom: 24px;
}

.stat-card {
  --tone: #4f46e5;
  --tone-soft: rgba(79, 70, 229, 0.1);
  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
  gap: 16px;
  min-width: 0;
  min-height: 112px;
  padding: 20px;
  background: #fff;
  border: 1px solid var(--line);
  border-radius: 16px;
  box-shadow: var(--shadow-sm);
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease),
    border-color 0.3s;
}

.stat-card::before {
  content: "";
  position: absolute;
  top: -38px;
  right: -38px;
  width: 120px;
  height: 120px;
  border-radius: 50%;
  background: var(--tone-soft);
  transition: transform 0.5s var(--ease);
}

.stat-card::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 4px;
  background: var(--tone);
  transform: scaleX(0.28);
  transform-origin: left;
  transition: transform 0.45s var(--ease);
}

.stat-card:nth-child(1) { --tone: #4f46e5; --tone-soft: rgba(79, 70, 229, 0.1); }
.stat-card:nth-child(2) { --tone: #10b981; --tone-soft: rgba(16, 185, 129, 0.12); }
.stat-card:nth-child(3) { --tone: #f59e0b; --tone-soft: rgba(245, 158, 11, 0.13); }
.stat-card:nth-child(4) { --tone: #a855f7; --tone-soft: rgba(168, 85, 247, 0.12); }

.stat-card:hover {
  transform: translateY(-6px);
  border-color: transparent;
  box-shadow: 0 16px 36px rgba(15, 23, 42, 0.12);
}

.stat-card:hover::before { transform: scale(1.5); }
.stat-card:hover::after { transform: scaleX(1); }

.stat-card > div:last-child {
  position: relative;
  z-index: 1;
  min-width: 0;
}

.stat-icon {
  position: relative;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  flex: 0 0 54px;
  width: 54px;
  height: 54px;
  border-radius: 16px;
  color: #fff;
  font-size: 23px;
  font-weight: 600;
  transition: transform 0.35s var(--ease);
}

.stat-card:hover .stat-icon { transform: rotate(-8deg) scale(1.1); }

.icon-blue {
  background: linear-gradient(135deg, #6366f1, #4f46e5);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.35);
}

.icon-green {
  background: linear-gradient(135deg, #34d399, #059669);
  box-shadow: 0 8px 16px rgba(16, 185, 129, 0.35);
}

.icon-orange {
  background: linear-gradient(135deg, #fbbf24, #f97316);
  box-shadow: 0 8px 16px rgba(245, 158, 11, 0.38);
}

.icon-purple {
  background: linear-gradient(135deg, #c084fc, #9333ea);
  box-shadow: 0 8px 16px rgba(168, 85, 247, 0.35);
}

.stat-label {
  margin-bottom: 6px;
  color: var(--muted);
  font-size: 12px;
  font-weight: 600;
  white-space: nowrap;
}

.stat-number {
  color: var(--navy);
  font-size: 28px;
  font-weight: 800;
  line-height: 1.1;
}

.stat-note {
  margin-top: 6px;
  color: #94a3b8;
  font-size: 11px;
}

/* =========================================================
   KHUNG DANH SÁCH
   ========================================================= */
.content-card {
  overflow: hidden;
  background: #fff;
  border: 1px solid var(--line);
  border-radius: 18px;
  box-shadow: 0 8px 30px rgba(15, 23, 42, 0.06);
}

.list-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  padding: 24px 26px 18px;
}

.list-heading h2 {
  margin: 0;
  color: var(--navy);
  font-size: 18px;
  font-weight: 800;
}

.list-heading p {
  margin: 7px 0 0;
  color: var(--muted);
  font-size: 12.5px;
}

.result-count {
  flex-shrink: 0;
  padding: 8px 14px;
  color: #fff;
  background: var(--grad-primary);
  border-radius: 999px;
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
  font-size: 12px;
  font-weight: 700;
}

/* =========================================================
   TÌM KIẾM / LỌC
   ========================================================= */
.filter-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 14px;
  padding: 16px 26px;
  border-top: 1px solid var(--line);
  border-bottom: 1px solid var(--line);
  background: #f8fafc;
}

.search-box {
  display: flex;
  align-items: center;
  gap: 9px;
  width: min(100%, 360px);
  height: 46px;
  padding: 0 14px;
  color: var(--muted);
  background: #fff;
  border: 1.5px solid var(--line);
  border-radius: 12px;
  transition: all 0.25s var(--ease);
}

.search-box:hover { border-color: #c7d2fe; }

.search-box:focus-within {
  color: var(--primary);
  border-color: var(--primary);
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}

.search-icon {
  font-size: 23px;
  line-height: 1;
  transform: rotate(-20deg);
}

.search-box input {
  width: 100%;
  min-width: 0;
  border: 0;
  outline: 0;
  color: var(--navy);
  background: transparent;
  font-size: 13px;
}

.search-box input::placeholder { color: #94a3b8; }

.clear-search {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  padding: 0;
  border: 0;
  border-radius: 50%;
  color: #64748b;
  background: #e2e8f0;
  font-size: 17px;
  line-height: 1;
  cursor: pointer;
  transition: all 0.2s;
}

.clear-search:hover {
  color: #fff;
  background: #f43f5e;
  transform: rotate(90deg);
}

.filter-tabs {
  display: flex;
  flex-wrap: wrap;
  gap: 4px;
  padding: 4px;
  background: var(--soft);
  border: 1px solid var(--line);
  border-radius: 12px;
}

.filter-tabs button {
  padding: 9px 16px;
  border: 0;
  border-radius: 9px;
  color: var(--muted);
  background: transparent;
  cursor: pointer;
  font-size: 12px;
  font-weight: 700;
  transition: all 0.25s var(--ease);
}

.filter-tabs button:hover:not(.active) {
  color: var(--primary);
  background: #e0e7ff;
}

.filter-tabs button.active {
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 5px 12px rgba(79, 70, 229, 0.35);
}

/* =========================================================
   BẢNG
   ========================================================= */
.table-wrapper {
  width: 100%;
  overflow-x: auto;
}

table {
  width: 100%;
  min-width: 980px;
  border-collapse: separate;
  border-spacing: 0;
  text-align: left;
}

thead th {
  padding: 15px 16px;
  color: #3730a3;
  background: linear-gradient(180deg, #eef2ff, #e0e7ff);
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 0.4px;
  white-space: nowrap;
}

th:first-child,
td:first-child { padding-left: 26px; }

th:last-child,
td:last-child { padding-right: 26px; }

td {
  padding: 15px 16px;
  border-bottom: 1px solid #eef2f7;
  color: #475569;
  font-size: 12.5px;
  vertical-align: middle;
  transition: background 0.25s;
}

tbody tr:hover td { background: #f5f7ff; }
tbody tr:hover td:first-child { box-shadow: inset 4px 0 0 var(--primary); }
tbody tr:last-child td { border-bottom: 0; }

.col-category { min-width: 220px; }

.category-name {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 190px;
}

.category-avatar {
  display: flex;
  align-items: center;
  justify-content: center;
  flex: 0 0 42px;
  width: 42px;
  height: 42px;
  border-radius: 13px;
  color: #fff;
  font-size: 16px;
  font-weight: 800;
  text-transform: uppercase;
  transition: transform 0.35s var(--ease), box-shadow 0.35s var(--ease);
}

tbody tr:hover .category-avatar {
  transform: rotate(-8deg) scale(1.1);
}

.avatar-blue {
  background: linear-gradient(135deg, #6366f1, #4f46e5);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.3);
}

.avatar-purple {
  background: linear-gradient(135deg, #c084fc, #9333ea);
  box-shadow: 0 8px 16px rgba(168, 85, 247, 0.3);
}

.avatar-green {
  background: linear-gradient(135deg, #34d399, #059669);
  box-shadow: 0 8px 16px rgba(16, 185, 129, 0.3);
}

.avatar-orange {
  background: linear-gradient(135deg, #fbbf24, #f97316);
  box-shadow: 0 8px 16px rgba(245, 158, 11, 0.3);
}

.avatar-pink {
  background: linear-gradient(135deg, #f472b6, #db2777);
  box-shadow: 0 8px 16px rgba(236, 72, 153, 0.3);
}

.avatar-cyan {
  background: linear-gradient(135deg, #22d3ee, #0891b2);
  box-shadow: 0 8px 16px rgba(6, 182, 212, 0.3);
}

.name-text {
  color: var(--navy);
  font-size: 13px;
  font-weight: 700;
  transition: color 0.2s;
}

tbody tr:hover .name-text { color: var(--primary); }

.name-sub {
  margin-top: 5px;
  color: #94a3b8;
  font-size: 10.5px;
}

.code-badge {
  display: inline-block;
  padding: 6px 10px;
  color: #4338ca;
  background: #e0e7ff;
  border-radius: 8px;
  font-family: Consolas, monospace;
  font-size: 11.5px;
  font-weight: 700;
}

.description-text {
  display: block;
  max-width: 230px;
  color: var(--muted);
  line-height: 1.6;
}

.text-center { text-align: center; }

.product-count {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 34px;
  min-height: 30px;
  padding: 0 10px;
  color: #fff;
  background: linear-gradient(135deg, #818cf8, #4f46e5);
  border-radius: 999px;
  box-shadow: 0 4px 10px rgba(79, 70, 229, 0.25);
  font-size: 12px;
  font-weight: 800;
  transition: transform 0.3s var(--ease);
}

tbody tr:hover .product-count { transform: scale(1.12); }

/* trạng thái */
.status-badge {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 6px 12px;
  border-radius: 999px;
  font-size: 11px;
  font-weight: 700;
  white-space: nowrap;
}

.status-active {
  color: #047857;
  background: #d1fae5;
}

.status-active .status-dot { animation: pulseDot 1.8s ease-in-out infinite; }

.status-inactive {
  color: #b45309;
  background: #fef3c7;
}

.status-dot {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: currentColor;
}

.date-text {
  color: var(--muted);
  white-space: nowrap;
}

/* =========================================================
   NÚT THAO TÁC
   ========================================================= */
.action-group {
  display: flex;
  justify-content: center;
  flex-wrap: nowrap;
  gap: 8px;
  min-width: 80px;
}

.action-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 34px;
  height: 34px;
  border: 0;
  border-radius: 10px;
  color: #fff;
  cursor: pointer;
  font-size: 17px;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease);
}

.edit-btn {
  background: linear-gradient(135deg, #6366f1, #2563eb);
  box-shadow: 0 5px 12px rgba(79, 70, 229, 0.3);
}

.edit-btn:hover {
  box-shadow: 0 10px 20px rgba(79, 70, 229, 0.45);
}

.delete-btn {
  background: var(--grad-danger);
  box-shadow: 0 5px 12px rgba(244, 63, 94, 0.3);
  font-size: 21px;
}

.delete-btn:hover {
  box-shadow: 0 10px 20px rgba(244, 63, 94, 0.45);
}

/* nút "hiện lại" nằm đầu nhóm và dùng chung class edit-btn: cho màu lục để phân biệt */
.action-group .edit-btn:first-child {
  background: var(--grad-success);
  box-shadow: 0 5px 12px rgba(16, 185, 129, 0.3);
}

.action-group .edit-btn:first-child:hover {
  box-shadow: 0 10px 20px rgba(16, 185, 129, 0.45);
}

.action-btn:hover {
  transform: translateY(-3px) scale(1.08);
}

.action-btn:active {
  transform: scale(0.92);
}

/* chân bảng */
.table-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 12px;
  padding: 15px 26px;
  border-top: 1px solid var(--line);
  background: #f8fafc;
  color: var(--muted);
  font-size: 12px;
}

.table-footer span:last-child {
  padding: 5px 12px;
  border-radius: 999px;
  color: #047857;
  background: #d1fae5;
  font-weight: 700;
}

/* =========================================================
   TRẠNG THÁI TRỐNG
   ========================================================= */
.empty-state {
  padding: 52px 16px;
  text-align: center;
}

.empty-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 72px;
  height: 72px;
  border-radius: 50%;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 32px;
}

.empty-state h3 {
  margin: 15px 0 7px;
  color: var(--navy);
  font-size: 15px;
  font-weight: 800;
}

.empty-state p {
  margin: 0 0 16px;
  color: var(--muted);
  font-size: 12.5px;
}

/* =========================================================
   MODAL
   ========================================================= */
.modal-overlay {
  position: fixed;
  inset: 0;
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 18px;
  background: rgba(15, 23, 42, 0.55);
  backdrop-filter: blur(4px);
  animation: overlayIn 0.25s ease both;
}

.modal-card {
  width: 100%;
  max-width: 500px;
  max-height: 90vh;
  overflow-y: auto;
  padding: 0 26px 26px;
  background: #fff;
  border-radius: 20px;
  box-shadow: 0 30px 80px rgba(15, 23, 42, 0.3);
  animation: modalIn 0.4s var(--ease) both;
}

.modal-heading {
  display: flex;
  justify-content: space-between;
  gap: 15px;
  margin: 0 -26px 24px;
  padding: 24px 26px;
  background: var(--grad-primary);
}

.modal-heading h2 {
  margin: 0;
  color: #fff;
  font-size: 20px;
  font-weight: 800;
}

.modal-heading p {
  margin: 7px 0 0;
  color: rgba(255, 255, 255, 0.85);
  font-size: 12.5px;
  line-height: 1.5;
}

.modal-close {
  width: 34px;
  height: 34px;
  flex-shrink: 0;
  border: 0;
  border-radius: 50%;
  color: #fff;
  background: rgba(255, 255, 255, 0.2);
  cursor: pointer;
  font-size: 22px;
  line-height: 1;
  transition: transform 0.3s var(--ease), background 0.2s;
}

.modal-close:hover {
  background: rgba(255, 255, 255, 0.35);
  transform: rotate(90deg);
}

/* form */
.form-group { margin-bottom: 18px; }

.form-group label {
  display: block;
  margin-bottom: 8px;
  color: #334155;
  font-size: 12.5px;
  font-weight: 700;
}

.form-group label span { color: #f43f5e; }

.form-group input,
.form-group textarea,
.form-group select {
  display: block;
  width: 100%;
  padding: 12px 14px;
  border: 1.5px solid var(--line);
  border-radius: 11px;
  outline: 0;
  color: var(--navy);
  background: #f8fafc;
  font-family: inherit;
  font-size: 13px;
  transition: all 0.25s var(--ease);
}

.form-group input:hover,
.form-group textarea:hover,
.form-group select:hover { border-color: #c7d2fe; }

.form-group input:focus,
.form-group textarea:focus,
.form-group select:focus {
  border-color: var(--primary);
  background: #fff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}

.form-group input[readonly] {
  color: #94a3b8;
  background: #eef2f7;
  cursor: not-allowed;
}

.form-group textarea {
  resize: vertical;
  min-height: 90px;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  margin-top: 25px;
  padding-top: 18px;
  border-top: 1px solid var(--line);
}

/* =========================================================
   TOAST
   ========================================================= */
.toast-message {
  position: fixed;
  right: 25px;
  bottom: 25px;
  z-index: 1100;
  display: flex;
  align-items: center;
  gap: 11px;
  max-width: calc(100vw - 32px);
  padding: 14px 20px 14px 14px;
  border-radius: 14px;
  color: #fff;
  background: linear-gradient(135deg, #059669, #10b981);
  box-shadow: 0 14px 34px rgba(5, 150, 105, 0.4);
  font-size: 13px;
  font-weight: 600;
  animation: toastIn 0.4s var(--ease) both;
}

.toast-message span {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 26px;
  height: 26px;
  border-radius: 50%;
  color: #059669;
  background: #fff;
  font-weight: 800;
}

/* thanh cuộn gọn */
.table-wrapper::-webkit-scrollbar { height: 8px; }
.table-wrapper::-webkit-scrollbar-thumb { border-radius: 8px; background: #c7d2fe; }
.table-wrapper::-webkit-scrollbar-thumb:hover { background: #818cf8; }

/* =========================================================
   RESPONSIVE
   ========================================================= */
@media (max-width: 1150px) {
  .stats-grid { grid-template-columns: repeat(2, minmax(0, 1fr)); }
}

@media (max-width: 700px) {
  .page-heading { align-items: flex-start; flex-direction: column; }
  .page-heading h1 { font-size: 24px; }
  .stats-grid { gap: 10px; }
  .stat-card { align-items: flex-start; flex-direction: column; gap: 10px; padding: 14px; }
  .stat-icon { width: 42px; height: 42px; flex-basis: 42px; border-radius: 12px; font-size: 18px; }
  .stat-number { font-size: 23px; }
  .filter-bar { align-items: stretch; padding-right: 15px; padding-left: 15px; }
  .search-box { width: 100%; }
  .filter-tabs { align-self: flex-start; }
  .list-heading { padding: 20px 15px; }
  .table-footer { padding: 14px 15px; }
  .modal-card { padding: 0 18px 20px; }
  .modal-heading { margin: 0 -18px 20px; padding: 20px 18px; }
}

/* Tôn trọng người dùng tắt hiệu ứng chuyển động */
@media (prefers-reduced-motion: reduce) {
  .category-page *,
  .category-page *::before,
  .category-page *::after {
    animation: none !important;
    transition: none !important;
  }
}
</style>