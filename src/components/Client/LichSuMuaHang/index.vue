<template>
  <main class="history-page">
    <div class="history-glow glow-left"></div>
    <div class="history-glow glow-right"></div>

    <div class="history-shell">
      <!-- HEADER -->
      <header class="history-header">
        <div>
          <span class="history-kicker">
            <span class="kicker-line"></span>
            TECHSHOP / TÀI KHOẢN
          </span>

          <h1>Đơn hàng của bạn<span>.</span></h1>

          <p>
            Theo dõi trạng thái, kiểm tra thông tin và quản lý các đơn hàng đã
            đặt tại TECHSHOP.
          </p>
        </div>

        <button
          class="refresh-button"
          type="button"
          :disabled="loading"
          @click="refreshAll"
        >
          <i
            class="fa-solid fa-rotate-right"
            :class="{ spinning: loading }"
          ></i>
          {{ loading ? "Đang tải..." : "Làm mới" }}
        </button>
      </header>

      <!-- THỐNG KÊ -->
      <section class="stats-grid">
        <article class="stat-card">
          <div class="stat-icon icon-total">
            <i class="fa-solid fa-receipt"></i>
          </div>

          <div>
            <span>Tổng đơn hàng</span>
            <strong>{{ orders.length }}</strong>
            <small>Tất cả đơn đã ghi nhận</small>
          </div>
        </article>

        <article class="stat-card">
          <div class="stat-icon icon-pending">
            <i class="fa-regular fa-clock"></i>
          </div>

          <div>
            <span>Chờ xác nhận</span>
            <strong>{{ countStatus("ChoXacNhan") }}</strong>
            <small>Đang chờ cửa hàng xử lý</small>
          </div>
        </article>

        <article class="stat-card">
          <div class="stat-icon icon-shipping">
            <i class="fa-solid fa-truck-fast"></i>
          </div>

          <div>
            <span>Đang giao</span>
            <strong>{{ countStatus("DangGiao") }}</strong>
            <small>Đơn đang trên đường đến bạn</small>
          </div>
        </article>

        <article class="stat-card">
          <div class="stat-icon icon-complete">
            <i class="fa-solid fa-circle-check"></i>
          </div>

          <div>
            <span>Đã giao</span>
            <strong>{{ countStatus("DaGiao") }}</strong>
            <small>Đơn đã hoàn thành giao hàng</small>
          </div>
        </article>
      </section>

      <!-- TÌM KIẾM -->
      <section class="orders-toolbar">
        <div class="toolbar-heading">
          <div>
            <span class="section-kicker">MY ORDERS</span>
            <h2>Lịch sử mua hàng</h2>
          </div>

          <span class="result-count">
            {{ filteredOrders.length }} đơn hàng
          </span>
        </div>

        <label class="search-orders">
          <i class="fa-solid fa-magnifying-glass"></i>

          <input
            v-model.trim="keyword"
            type="search"
            placeholder="Tìm theo mã đơn, tên người nhận..."
          />

          <button
            v-if="keyword"
            type="button"
            aria-label="Xóa nội dung tìm kiếm"
            @click="keyword = ''"
          >
            <i class="fa-solid fa-xmark"></i>
          </button>
        </label>

        <!-- LỌC TRẠNG THÁI -->
        <div class="filter-tabs">
          <button
            v-for="tab in statusFilters"
            :key="tab.key"
            type="button"
            class="filter-tab"
            :class="{ active: activeFilter === tab.key }"
            @click="activeFilter = tab.key"
          >
            {{ tab.label }}

            <span>{{ countFilter(tab.key) }}</span>
          </button>
        </div>
      </section>

      <!-- LỖI -->
      <div v-if="error" class="history-alert">
        <i class="fa-solid fa-circle-exclamation"></i>

        <span>{{ error }}</span>

        <button type="button" aria-label="Đóng thông báo" @click="error = ''">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <div v-if="requestLoadError" class="history-alert request-load-alert">
        <i class="fa-solid fa-triangle-exclamation"></i>
        <span>{{ requestLoadError }}</span>
        <button type="button" class="request-retry-button" @click="loadCustomerRequests">
          Tải lại
        </button>
      </div>

      <!-- LOADING -->
      <section v-if="loading && orders.length === 0" class="history-state">
        <div class="state-spinner"></div>
        <h3>Đang tải đơn hàng</h3>
        <p>Đang kết nối với hệ thống TECHSHOP...</p>
      </section>

      <!-- CHƯA ĐĂNG NHẬP / LỖI TẢI -->
      <section v-else-if="error && orders.length === 0" class="history-state">
        <div class="state-icon state-error">
          <i class="fa-solid fa-box-open"></i>
        </div>

        <h3>Chưa thể tải lịch sử mua hàng</h3>
        <p>{{ error }}</p>

        <button class="primary-button" type="button" @click="loadOrders">
          Thử lại
        </button>
      </section>

      <!-- KHÔNG CÓ ĐƠN -->
      <section
        v-else-if="filteredOrders.length === 0"
        class="history-state empty-state"
      >
        <div class="state-icon">
          <i class="fa-solid fa-box-open"></i>
        </div>

        <h3>
          {{
            orders.length
              ? "Không tìm thấy đơn hàng phù hợp"
              : "Bạn chưa có đơn hàng nào"
          }}
        </h3>

        <p>
          {{
            orders.length
              ? "Thử đổi trạng thái hoặc từ khóa tìm kiếm."
              : "Các đơn hàng bạn đặt sẽ xuất hiện tại đây."
          }}
        </p>

        <button
          v-if="orders.length"
          class="secondary-button"
          type="button"
          @click="resetFilters"
        >
          Xóa bộ lọc
        </button>

        <button
          v-else
          class="primary-button"
          type="button"
          @click="$router.push('/')"
        >
          Khám phá sản phẩm
          <i class="fa-solid fa-arrow-right"></i>
        </button>
      </section>

      <!-- DANH SÁCH ĐƠN -->
      <section v-else class="orders-list">
        <article
          v-for="order in filteredOrders"
          :key="order.MaDH"
          class="order-card"
        >
          <!-- ĐẦU THẺ -->
          <div class="order-card-header">
            <div class="order-id-block">
              <span class="order-id-label">MÃ ĐƠN HÀNG</span>
              <strong>#{{ order.MaDH }}</strong>
              <small>{{ formatDate(order.NgayDat) }}</small>
            </div>

            <span
              class="status-badge"
              :class="statusInfo(order.TrangThaiDH).className"
            >
              <i :class="statusInfo(order.TrangThaiDH).icon"></i>
              {{ statusInfo(order.TrangThaiDH).label }}
            </span>
          </div>

          <!-- TIẾN TRÌNH -->
          <div v-if="order.TrangThaiDH !== 'DaHuy'" class="order-progress">
            <div
              v-for="step in progressSteps"
              :key="step.key"
              class="progress-step"
              :class="progressStepClass(order, step.key)"
            >
              <div class="progress-marker">
                <i
                  v-if="isStepCompleted(order, step.key)"
                  class="fa-solid fa-check"
                ></i>

                <span v-else></span>
              </div>

              <small>{{ step.label }}</small>
            </div>
          </div>

          <!-- ĐÃ HỦY -->
          <div v-else class="cancelled-banner">
            <i class="fa-solid fa-circle-xmark"></i>

            <div>
              <strong>Đơn hàng đã hủy</strong>

              <span> Đơn hàng không còn trong quá trình giao. </span>

              <span v-if="order.LyDoHuy"> Lý do hủy: {{ order.LyDoHuy }} </span>

              <small v-if="order.ThoiGianHuy">
                Thời gian hủy: {{ formatDate(order.ThoiGianHuy) }}
              </small>
            </div>
          </div>

          <!-- DANH SÁCH SẢN PHẨM -->
          <div class="order-products">
            <div
              v-for="(item, index) in getOrderItems(order).slice(0, 2)"
              :key="item.MaCTDH || item.MaSP || index"
              class="order-product"
            >
              <div class="order-product-image">
                <img
                  v-if="productImage(item)"
                  :src="imageUrl(productImage(item))"
                  :alt="productName(item)"
                  @error="hideBrokenImage"
                />

                <div v-else class="product-image-placeholder">
                  <i class="fa-solid fa-box"></i>
                </div>
              </div>

              <div class="order-product-info">
                <strong>{{ productName(item) }}</strong>

                <span> Số lượng: {{ Number(item.SoLuong || 0) }} </span>

                <button
                  v-if="
                    ['DaGiao', 'DaNhanHang'].includes(order.TrangThaiDH) &&
                    item.MaSP
                  "
                  type="button"
                  class="review-product-button"
                  @click="goToReview(order, item)"
                >
                  <i class="fa-solid fa-star"></i>
                  Đánh giá sản phẩm
                </button>


<div class="customer-request-actions">
  <section v-if="timBaoHanhGanNhat(item)" class="request-tracking-card warranty-tracking-card">
    <div class="request-tracking-heading">
      <div>
        <span class="request-tracking-kicker">BẢO HÀNH · #{{ timBaoHanhGanNhat(item).MaYCBH }}</span>
      </div>
      <span class="request-status-pill" :class="requestStatusClass(timBaoHanhGanNhat(item).TrangThai)">
        {{ tenTrangThaiYeuCau(timBaoHanhGanNhat(item).TrangThai) }}
      </span>
    </div>
    <p class="request-tracking-description">
      {{ moTaTrangThaiYeuCau('bao-hanh', timBaoHanhGanNhat(item)) }}
    </p>
    <div v-if="timBaoHanhGanNhat(item).NgayYeuCau" class="request-tracking-date">
      Gửi yêu cầu: {{ formatDate(timBaoHanhGanNhat(item).NgayYeuCau) }}
    </div>

    <div v-if="timBaoHanhGanNhat(item).NgayBaoGia" class="request-quote-summary">
      <div><span>Chi phí sửa chữa</span><strong>{{ formatMoney(timBaoHanhGanNhat(item).ChiPhiDuKien) }}</strong></div>
      <div><span>Shop hỗ trợ ({{ Number(timBaoHanhGanNhat(item).TyLeShopHoTroApDung || 0) }}%)</span><strong>{{ formatMoney(timBaoHanhGanNhat(item).SoTienShopHoTro) }}</strong></div>
      <div class="request-customer-amount"><span>Bạn cần thanh toán</span><strong>{{ formatMoney(timBaoHanhGanNhat(item).SoTienKhachTra) }}</strong></div>
    </div>
    <p v-if="timBaoHanhGanNhat(item).GhiChuBaoGia" class="request-tracking-note">
      Ghi chú của cửa hàng: {{ timBaoHanhGanNhat(item).GhiChuBaoGia }}
    </p>
    <div v-if="timBaoHanhGanNhat(item).TrangThai === 'ChoKhachXacNhanBaoGia'" class="request-customer-actions">
      <button type="button" class="customer-action-button warranty-request-button" @click.stop="moModalPhanHoiBaoGia(timBaoHanhGanNhat(item))">
        <i class="fa-solid fa-file-invoice-dollar"></i> Xem báo giá và phản hồi
      </button>
    </div>
    <div v-if="timBaoHanhGanNhat(item).TrangThai === 'ChoThanhToan'" class="request-customer-actions">
      <button type="button" class="customer-action-button warranty-request-button" @click.stop="moModalThongBaoThanhToan(timBaoHanhGanNhat(item))">
        <i class="fa-solid fa-credit-card"></i> Thông báo đã thanh toán
      </button>
    </div>
    <div v-if="timBaoHanhGanNhat(item).TrangThai === 'ChoXacNhanThanhToan'" class="request-waiting-note">
      <i class="fa-regular fa-clock"></i> Shop đang kiểm tra khoản thanh toán của bạn.
    </div>
    <div v-if="timBaoHanhGanNhat(item).TrangThai === 'KhachTuChoiBaoGia'" class="request-waiting-note">
      <i class="fa-solid fa-truck"></i> Cửa hàng đang sắp xếp gửi trả sản phẩm cho bạn.
    </div>
    <p v-if="timBaoHanhGanNhat(item).LyDoTuChoi" class="request-tracking-note request-rejection-note">
      Lý do từ chối: {{ timBaoHanhGanNhat(item).LyDoTuChoi }}
    </p>
    <p v-if="timBaoHanhGanNhat(item).KetQuaXuLy" class="request-tracking-note">
      Kết quả bảo hành: {{ timBaoHanhGanNhat(item).KetQuaXuLy }}
    </p>
  </section>

  <section v-if="timDoiTraGanNhat(item)" class="request-tracking-card return-tracking-card">
    <div class="request-tracking-heading">
      <div>
        <span class="request-tracking-kicker">
          {{ timDoiTraGanNhat(item).LoaiYeuCau === 'DoiHang' ? 'ĐỔI HÀNG' : 'TRẢ HÀNG / HOÀN TIỀN' }}
          · #{{ timDoiTraGanNhat(item).MaYCDT }}
        </span>
      </div>
      <span class="request-status-pill" :class="requestStatusClass(timDoiTraGanNhat(item).TrangThai)">
        {{ tenTrangThaiYeuCau(timDoiTraGanNhat(item).TrangThai) }}
      </span>
    </div>
    <p class="request-tracking-description">
      {{ moTaTrangThaiYeuCau('doi-tra', timDoiTraGanNhat(item)) }}
    </p>
    <div v-if="timDoiTraGanNhat(item).NgayYeuCau" class="request-tracking-date">
      Gửi yêu cầu: {{ formatDate(timDoiTraGanNhat(item).NgayYeuCau) }}
    </div>
    <div v-if="timDoiTraGanNhat(item).LoaiYeuCau === 'DoiHang' && timDoiTraGanNhat(item).san_pham_doi" class="request-exchange-product">
      Sản phẩm đổi: <strong>{{ timDoiTraGanNhat(item).san_pham_doi.TenSP }}</strong>
    </div>
    <div v-if="timDoiTraGanNhat(item).LoaiYeuCau === 'TraHangHoanTien' && timDoiTraGanNhat(item).SoTienHoan !== null && timDoiTraGanNhat(item).SoTienHoan !== undefined" class="request-refund-summary">
      <div><span>Số tiền hoàn</span><strong>{{ formatMoney(timDoiTraGanNhat(item).SoTienHoan) }}</strong></div>
      <div v-if="timDoiTraGanNhat(item).PhuongThucHoanTien"><span>Phương thức hoàn</span><strong>{{ timDoiTraGanNhat(item).PhuongThucHoanTien }}</strong></div>
      <small v-if="timDoiTraGanNhat(item).NgayHoanTien">Ngày hoàn tiền: {{ formatDate(timDoiTraGanNhat(item).NgayHoanTien) }}</small>
    </div>
    <p v-if="timDoiTraGanNhat(item).LyDoTuChoi" class="request-tracking-note request-rejection-note">
      Lý do từ chối: {{ timDoiTraGanNhat(item).LyDoTuChoi }}
    </p>
  </section>

  <template v-if="order.TrangThaiDH === 'DaGiao'">
    <button
      v-if="kiemTraChinhSachBaoHanh(item, order).hopLe && !kiemTraChinhSachBaoHanh(item, order).hetHan && !timBaoHanhDangXuLy(item)"
      type="button"
      class="customer-action-button warranty-request-button"
      @click.stop="moFormBaoHanh(order, item)"
    >
      <i class="fa-solid fa-shield-halved"></i>
      {{ timBaoHanhGanNhat(item) ? 'Gửi yêu cầu bảo hành mới' : 'Yêu cầu bảo hành' }}
    </button>
    <span v-else-if="!timBaoHanhGanNhat(item) && kiemTraChinhSachBaoHanh(item, order).hetHan" class="request-status-inline request-status-muted">
      Đã hết hạn bảo hành
    </span>
    <span v-else-if="!timBaoHanhGanNhat(item) && !kiemTraChinhSachBaoHanh(item, order).hopLe" class="request-status-inline request-status-muted">
      Chưa có dữ liệu bảo hành
    </span>

    <button
      v-if="!timDoiTraDangXuLy(item)"
      type="button"
      class="customer-action-button return-request-button"
      @click.stop="moFormDoiTra(order, item)"
    >
      <i class="fa-solid fa-arrows-rotate"></i>
      {{ timDoiTraGanNhat(item) ? 'Gửi yêu cầu đổi trả mới' : 'Yêu cầu đổi trả' }}
    </button>

    <button
      v-if="kiemTraMuaLai(item).duocMua"
      type="button"
      class="repurchase-button"
      @click.stop="muaLai(order, item)"
    >
      <i class="fa-solid fa-cart-shopping"></i> Mua lại
    </button>
    <span v-else class="repurchase-unavailable">{{ kiemTraMuaLai(item).lyDo }}</span>
  </template>
</div>

              </div>

              <strong class="order-product-price">
                {{
                  formatMoney(
                    Number(item.GiaBan || 0) * Number(item.SoLuong || 0)
                  )
                }}
              </strong>
            </div>

            <div v-if="getOrderItems(order).length > 2" class="more-products">
              Và thêm {{ getOrderItems(order).length - 2 }} sản phẩm khác
            </div>

            <div
              v-if="getOrderItems(order).length === 0"
              class="missing-details"
            >
              Chưa có dữ liệu chi tiết sản phẩm của đơn hàng này.
            </div>
          </div>

          <!-- TỔNG TIỀN -->
          <div class="order-card-footer">
            <div class="order-summary-left">
              <span> {{ getOrderItems(order).length }} dòng sản phẩm </span>

              <small> {{ totalUnits(order) }} sản phẩm </small>
            </div>

            <div class="order-total">
              <span>Thành tiền</span>
              <strong>{{ formatMoney(order.TongTien) }}</strong>
            </div>
          </div>

          <!-- NÚT THAO TÁC -->
          <div class="order-actions">
            <button
              type="button"
              class="secondary-button"
              @click="openDetails(order)"
            >
              <i class="fa-regular fa-file-lines"></i>
              Xem chi tiết
            </button>

            <button
              v-if="canCancel(order)"
              type="button"
              class="cancel-button"
              :disabled="cancelingId === String(order.MaDH)"
              @click="askCancel(order)"
            >
              <i class="fa-regular fa-trash-can"></i>

              {{
                cancelingId === String(order.MaDH)
                  ? "Đang hủy..."
                  : "Hủy đơn hàng"
              }}
            </button>
          </div>
        </article>
      </section>

      <teleport to="body">
        <!-- CHI TIẾT ĐƠN HÀNG -->
        <div
          v-if="selectedOrder"
          class="modal-backdrop-custom"
          @click.self="selectedOrder = null"
        >
          <section
            class="order-modal"
            role="dialog"
            aria-modal="true"
            aria-label="Chi tiết đơn hàng"
          >
            <header class="modal-header">
              <div>
                <span class="section-kicker">ORDER DETAILS</span>
                <h2>Đơn hàng #{{ selectedOrder.MaDH }}</h2>
                <p>{{ formatDate(selectedOrder.NgayDat) }}</p>
              </div>

              <button
                class="modal-close"
                type="button"
                aria-label="Đóng"
                @click="selectedOrder = null"
              >
                <i class="fa-solid fa-xmark"></i>
              </button>
            </header>

            <div class="modal-content">
              <!-- TRẠNG THÁI -->
              <div class="detail-status-panel">
                <span
                  class="status-badge"
                  :class="statusInfo(selectedOrder.TrangThaiDH).className"
                >
                  <i :class="statusInfo(selectedOrder.TrangThaiDH).icon"></i>
                  {{ statusInfo(selectedOrder.TrangThaiDH).label }}
                </span>

                <p>{{ statusInfo(selectedOrder.TrangThaiDH).description }}</p>
              </div>

              <!-- THÔNG TIN NHẬN HÀNG -->
              <section class="detail-section">
                <h3>
                  <i class="fa-solid fa-location-dot"></i>
                  Thông tin nhận hàng
                </h3>

                <div class="detail-info-grid">
                  <div>
                    <span>Người nhận</span>
                    <strong>{{ selectedOrder.TenNguoiNhan || "—" }}</strong>
                  </div>

                  <div>
                    <span>Số điện thoại</span>
                    <strong>{{ selectedOrder.SoDienThoaiNhan || "—" }}</strong>
                  </div>

                  <div class="detail-full">
                    <span>Địa chỉ giao hàng</span>
                    <strong>{{ selectedOrder.DiaChiGiaoHang || "—" }}</strong>
                  </div>
                </div>
              </section>
                            <!-- SẢN PHẨM -->
                            <section class="detail-section">
                <h3>
                  <i class="fa-solid fa-box-open"></i>
                  Sản phẩm trong đơn
                </h3>

                <div
                  v-for="(item, index) in getOrderItems(selectedOrder)"
                  :key="item.MaCTDH || item.MaSP || index"
                  class="detail-product"
                >
                  <div class="detail-product-image">
                    <img
                      v-if="productImage(item)"
                      :src="imageUrl(productImage(item))"
                      :alt="productName(item)"
                      @error="hideBrokenImage"
                    />

                    <div v-else class="product-image-placeholder">
                      <i class="fa-solid fa-box"></i>
                    </div>
                  </div>

                  <div class="detail-product-copy">
                    <strong>{{ productName(item) }}</strong>
                    <span>Số lượng: {{ Number(item.SoLuong || 0) }}</span>
                    <small> {{ formatMoney(item.GiaBan) }} / sản phẩm </small>
                    <button
                      v-if="
                        ['DaGiao', 'DaNhanHang'].includes(
                          selectedOrder.TrangThaiDH
                        ) && item.MaSP
                      "
                      type="button"
                      class="review-product-button"
                      @click="goToReview(selectedOrder, item)"
                    >
                      <i class="fa-solid fa-star"></i>
                      Đánh giá sản phẩm
                    </button>

                    <!-- THÔNG TIN BẢO HÀNH THEO SNAPSHOT LÚC MUA -->
                    <section
                      v-if="selectedOrder.TrangThaiDH === 'DaGiao'"
                      class="warranty-snapshot"
                    >
                      <template v-if="kiemTraChinhSachBaoHanh(item, selectedOrder).hopLe">
                        <div class="warranty-snapshot-heading">
                          <i class="fa-solid fa-shield-halved"></i>
                          <strong>Bảo hành {{ item.ThoiHanBaoHanhThang }} tháng</strong>
                        </div>
                        <div class="warranty-snapshot-grid">
                          <div>
                            <span>Tổng thời hạn</span>
                            <strong>{{ item.ThoiHanBaoHanhThang }} tháng</strong>
                          </div>
                          <div>
                            <span>Hỗ trợ 100%</span>
                            <strong>{{ item.ThoiHanMienPhiThang }} tháng</strong>
                          </div>
                          <div>
                            <span>Shop hỗ trợ sau miễn phí</span>
                            <strong>{{ item.TyLeShopHoTro }}%</strong>
                          </div>
                          <div>
                            <span>Khách chịu phần còn lại</span>
                            <strong>{{ 100 - Number(item.TyLeShopHoTro) }}%</strong>
                          </div>
                          <div v-if="selectedOrder.NgayGiao" class="warranty-expiry">
                            <span>Hạn bảo hành đến</span>
                            <strong>{{ formatDateOnly(tinhNgayHetHan(selectedOrder.NgayGiao, item.ThoiHanBaoHanhThang)) }}</strong>
                          </div>
                        </div>
                        <p class="warranty-note">
                          Chính sách lấy từ chi tiết đơn hàng tại thời điểm mua, không thay đổi theo chính sách sản phẩm hiện tại.
                        </p>
                      </template>
                      <div v-else class="warranty-policy-missing">
                        <i class="fa-solid fa-circle-info"></i>
                        Đơn hàng chưa lưu đầy đủ chính sách bảo hành tại thời điểm mua.
                      </div>
                    </section>


<div class="customer-request-actions">
  <section v-if="timBaoHanhGanNhat(item)" class="request-tracking-card warranty-tracking-card">
    <div class="request-tracking-heading">
      <div>
        <span class="request-tracking-kicker">BẢO HÀNH · #{{ timBaoHanhGanNhat(item).MaYCBH }}</span>
      </div>
      <span class="request-status-pill" :class="requestStatusClass(timBaoHanhGanNhat(item).TrangThai)">
        {{ tenTrangThaiYeuCau(timBaoHanhGanNhat(item).TrangThai) }}
      </span>
    </div>
    <p class="request-tracking-description">
      {{ moTaTrangThaiYeuCau('bao-hanh', timBaoHanhGanNhat(item)) }}
    </p>
    <div v-if="timBaoHanhGanNhat(item).NgayYeuCau" class="request-tracking-date">
      Gửi yêu cầu: {{ formatDate(timBaoHanhGanNhat(item).NgayYeuCau) }}
    </div>

    <div v-if="timBaoHanhGanNhat(item).NgayBaoGia" class="request-quote-summary">
      <div><span>Chi phí sửa chữa</span><strong>{{ formatMoney(timBaoHanhGanNhat(item).ChiPhiDuKien) }}</strong></div>
      <div><span>Shop hỗ trợ ({{ Number(timBaoHanhGanNhat(item).TyLeShopHoTroApDung || 0) }}%)</span><strong>{{ formatMoney(timBaoHanhGanNhat(item).SoTienShopHoTro) }}</strong></div>
      <div class="request-customer-amount"><span>Bạn cần thanh toán</span><strong>{{ formatMoney(timBaoHanhGanNhat(item).SoTienKhachTra) }}</strong></div>
    </div>
    <p v-if="timBaoHanhGanNhat(item).GhiChuBaoGia" class="request-tracking-note">
      Ghi chú của cửa hàng: {{ timBaoHanhGanNhat(item).GhiChuBaoGia }}
    </p>
    <div v-if="timBaoHanhGanNhat(item).TrangThai === 'ChoKhachXacNhanBaoGia'" class="request-customer-actions">
      <button type="button" class="customer-action-button warranty-request-button" @click.stop="moModalPhanHoiBaoGia(timBaoHanhGanNhat(item))">
        <i class="fa-solid fa-file-invoice-dollar"></i> Xem báo giá và phản hồi
      </button>
    </div>
    <div v-if="timBaoHanhGanNhat(item).TrangThai === 'ChoThanhToan'" class="request-customer-actions">
      <button type="button" class="customer-action-button warranty-request-button" @click.stop="moModalThongBaoThanhToan(timBaoHanhGanNhat(item))">
        <i class="fa-solid fa-credit-card"></i> Thông báo đã thanh toán
      </button>
    </div>
    <div v-if="timBaoHanhGanNhat(item).TrangThai === 'ChoXacNhanThanhToan'" class="request-waiting-note">
      <i class="fa-regular fa-clock"></i> Shop đang kiểm tra khoản thanh toán của bạn.
    </div>
    <div v-if="timBaoHanhGanNhat(item).TrangThai === 'KhachTuChoiBaoGia'" class="request-waiting-note">
      <i class="fa-solid fa-truck"></i> Cửa hàng đang sắp xếp gửi trả sản phẩm cho bạn.
    </div>
    <p v-if="timBaoHanhGanNhat(item).LyDoTuChoi" class="request-tracking-note request-rejection-note">
      Lý do từ chối: {{ timBaoHanhGanNhat(item).LyDoTuChoi }}
    </p>
    <p v-if="timBaoHanhGanNhat(item).KetQuaXuLy" class="request-tracking-note">
      Kết quả bảo hành: {{ timBaoHanhGanNhat(item).KetQuaXuLy }}
    </p>
  </section>

  <section v-if="timDoiTraGanNhat(item)" class="request-tracking-card return-tracking-card">
    <div class="request-tracking-heading">
      <div>
        <span class="request-tracking-kicker">
          {{ timDoiTraGanNhat(item).LoaiYeuCau === 'DoiHang' ? 'ĐỔI HÀNG' : 'TRẢ HÀNG / HOÀN TIỀN' }}
          · #{{ timDoiTraGanNhat(item).MaYCDT }}
        </span>
      </div>
      <span class="request-status-pill" :class="requestStatusClass(timDoiTraGanNhat(item).TrangThai)">
        {{ tenTrangThaiYeuCau(timDoiTraGanNhat(item).TrangThai) }}
      </span>
    </div>
    <p class="request-tracking-description">
      {{ moTaTrangThaiYeuCau('doi-tra', timDoiTraGanNhat(item)) }}
    </p>
    <div v-if="timDoiTraGanNhat(item).NgayYeuCau" class="request-tracking-date">
      Gửi yêu cầu: {{ formatDate(timDoiTraGanNhat(item).NgayYeuCau) }}
    </div>
    <div v-if="timDoiTraGanNhat(item).LoaiYeuCau === 'DoiHang' && timDoiTraGanNhat(item).san_pham_doi" class="request-exchange-product">
      Sản phẩm đổi: <strong>{{ timDoiTraGanNhat(item).san_pham_doi.TenSP }}</strong>
    </div>
    <div v-if="timDoiTraGanNhat(item).LoaiYeuCau === 'TraHangHoanTien' && timDoiTraGanNhat(item).SoTienHoan !== null && timDoiTraGanNhat(item).SoTienHoan !== undefined" class="request-refund-summary">
      <div><span>Số tiền hoàn</span><strong>{{ formatMoney(timDoiTraGanNhat(item).SoTienHoan) }}</strong></div>
      <div v-if="timDoiTraGanNhat(item).PhuongThucHoanTien"><span>Phương thức hoàn</span><strong>{{ timDoiTraGanNhat(item).PhuongThucHoanTien }}</strong></div>
      <small v-if="timDoiTraGanNhat(item).NgayHoanTien">Ngày hoàn tiền: {{ formatDate(timDoiTraGanNhat(item).NgayHoanTien) }}</small>
    </div>
    <p v-if="timDoiTraGanNhat(item).LyDoTuChoi" class="request-tracking-note request-rejection-note">
      Lý do từ chối: {{ timDoiTraGanNhat(item).LyDoTuChoi }}
    </p>
  </section>

  <template v-if="selectedOrder.TrangThaiDH === 'DaGiao'">
    <button
      v-if="kiemTraChinhSachBaoHanh(item, selectedOrder).hopLe && !kiemTraChinhSachBaoHanh(item, selectedOrder).hetHan && !timBaoHanhDangXuLy(item)"
      type="button"
      class="customer-action-button warranty-request-button"
      @click.stop="moFormBaoHanh(selectedOrder, item)"
    >
      <i class="fa-solid fa-shield-halved"></i>
      {{ timBaoHanhGanNhat(item) ? 'Gửi yêu cầu bảo hành mới' : 'Yêu cầu bảo hành' }}
    </button>
    <span v-else-if="!timBaoHanhGanNhat(item) && kiemTraChinhSachBaoHanh(item, selectedOrder).hetHan" class="request-status-inline request-status-muted">
      Đã hết hạn bảo hành
    </span>
    <span v-else-if="!timBaoHanhGanNhat(item) && !kiemTraChinhSachBaoHanh(item, selectedOrder).hopLe" class="request-status-inline request-status-muted">
      Chưa có dữ liệu bảo hành
    </span>

    <button
      v-if="!timDoiTraDangXuLy(item)"
      type="button"
      class="customer-action-button return-request-button"
      @click.stop="moFormDoiTra(selectedOrder, item)"
    >
      <i class="fa-solid fa-arrows-rotate"></i>
      {{ timDoiTraGanNhat(item) ? 'Gửi yêu cầu đổi trả mới' : 'Yêu cầu đổi trả' }}
    </button>

    <button
      v-if="kiemTraMuaLai(item).duocMua"
      type="button"
      class="repurchase-button"
      @click.stop="muaLai(selectedOrder, item)"
    >
      <i class="fa-solid fa-cart-shopping"></i> Mua lại
    </button>
    <span v-else class="repurchase-unavailable">{{ kiemTraMuaLai(item).lyDo }}</span>
  </template>
</div>

                  </div>

                  <strong>
                    {{
                      formatMoney(
                        Number(item.GiaBan || 0) * Number(item.SoLuong || 0)
                      )
                    }}
                  </strong>
                </div>
              </section>

              <!-- THANH TOÁN -->
              <section class="detail-section">
                <h3>
                  <i class="fa-regular fa-credit-card"></i>
                  Tổng kết đơn hàng
                </h3>

                <div class="detail-money-line">
                  <span>Phương thức thanh toán</span>
                  <strong>
                    {{
                      selectedOrder.PhuongThucThanhToan === "COD"
                        ? "Thanh toán khi nhận hàng (COD)"
                        : selectedOrder.PhuongThucThanhToan || "—"
                    }}
                  </strong>
                </div>

                <div
                  v-if="selectedOrder.voucher || selectedOrder.MaVoucher"
                  class="detail-money-line"
                >
                  <span>Mã voucher</span>
                  <strong>
                    {{
                      selectedOrder.voucher?.MaGiamGia || "Đã áp dụng voucher"
                    }}
                  </strong>
                </div>

                <div class="detail-total-line">
                  <span>Tổng thanh toán</span>
                  <strong>{{ formatMoney(selectedOrder.TongTien) }}</strong>
                </div>
              </section>
            </div>

            <footer class="modal-footer">
              <button
                class="secondary-button"
                type="button"
                @click="selectedOrder = null"
              >
                Đóng
              </button>

              <button
                v-if="canCancel(selectedOrder)"
                class="cancel-button"
                type="button"
                @click="askCancel(selectedOrder)"
              >
                <i class="fa-regular fa-trash-can"></i>
                Hủy đơn hàng
              </button>
            </footer>
          </section>
        </div>

        <!-- XÁC NHẬN HỦY -->
        <div
          v-if="orderToCancel"
          class="modal-backdrop-custom cancel-backdrop"
          @click.self="orderToCancel = null"
        >
          <section
            class="cancel-modal"
            role="dialog"
            aria-modal="true"
            aria-label="Xác nhận hủy đơn"
          >
            <div class="cancel-modal-icon">
              <i class="fa-solid fa-triangle-exclamation"></i>
            </div>

            <h2>Hủy đơn hàng?</h2>

            <p>
              Bạn có chắc muốn hủy đơn
              <strong>#{{ orderToCancel.MaDH }}</strong> không? Thao tác này
              không thể hoàn tác.
            </p>
            <div class="cancel-reason-field">
              <label for="cancelReason"> Lý do hủy đơn <span>*</span> </label>

              <textarea
                id="cancelReason"
                v-model.trim="cancelReason"
                rows="3"
                maxlength="255"
                placeholder="Nhập lý do bạn muốn hủy đơn hàng..."
                :disabled="!!cancelingId"
              ></textarea>

              <small>{{ cancelReason.length }}/255 ký tự</small>
            </div>
            <div class="cancel-modal-actions">
              <button
                class="secondary-button"
                type="button"
                :disabled="!!cancelingId"
                @click="orderToCancel = null"
              >
                Giữ đơn hàng
              </button>

              <button
                class="cancel-confirm-button"
                type="button"
                :disabled="!!cancelingId"
                @click="confirmCancel"
              >
                {{ cancelingId ? "Đang xử lý..." : "Xác nhận hủy" }}
              </button>
            </div>
          </section>
        </div>

        <!-- MODAL GỬI YÊU CẦU BẢO HÀNH / ĐỔI TRẢ -->
        <div
          v-if="requestModal.open"
          class="modal-backdrop-custom request-modal-backdrop"
          @click.self="dongFormYeuCau"
        >
          <section class="customer-request-modal" role="dialog" aria-modal="true" @click="showProductDropdown = false">
            <header class="customer-request-modal-header">
              <div>
                <span class="section-kicker">TECHSHOP CUSTOMER SERVICE</span>
                <h2>{{ requestModal.type === 'bao-hanh' ? 'Yêu cầu bảo hành' : 'Yêu cầu đổi trả' }}</h2>
                <p>
                  Đơn hàng #{{ requestModal.order?.MaDH }}
                  · {{ productName(requestModal.item || {}) }}
                </p>
              </div>
              <button
                type="button"
                class="modal-close"
                aria-label="Đóng biểu mẫu"
                :disabled="submittingRequest"
                @click="dongFormYeuCau"
              >
                <i class="fa-solid fa-xmark"></i>
              </button>
            </header>

            <div v-if="requestModal.type === 'bao-hanh'" class="request-info-note">
              <i class="fa-solid fa-shield-halved"></i>
              <span>
                Thời hạn bảo hành được tính từ ngày giao hàng. Shop hỗ trợ 100% trong thời gian miễn phí;
                sau đó áp dụng tỷ lệ hỗ trợ đã lưu trong đơn hàng.
              </span>
            </div>

            <div v-else class="request-info-note">
              <i class="fa-solid fa-circle-info"></i>
              <span>
                Thời hạn thông thường: đổi hàng 1 ngày, trả hàng hoàn tiền 3 ngày kể từ ngày giao.
                Quá hạn vẫn có thể gửi để nhân viên xem xét, không tự động bị từ chối.
              </span>
            </div>

            <template v-if="requestModal.type === 'bao-hanh'">
              <label class="customer-request-field">
                Tình trạng lỗi sản phẩm <b>*</b>
                <textarea
                  v-model.trim="formYeuCauBaoHanh.TinhTrangLoi"
                  rows="5"
                  maxlength="5000"
                  placeholder="Mô tả chi tiết lỗi, thời điểm xảy ra và biểu hiện của sản phẩm..."
                ></textarea>
                <small>{{ formYeuCauBaoHanh.TinhTrangLoi.length }}/5000 ký tự</small>
              </label>

            </template>

            <template v-else>
              <label class="customer-request-field">
                Loại yêu cầu <b>*</b>
                <select v-model="formYeuCauDoiTra.LoaiYeuCau" @change="showProductDropdown = false">
                  <option value="DoiHang">Đổi hàng</option>
                  <option value="TraHangHoanTien">Trả hàng hoàn tiền</option>
                </select>
              </label>

              <label class="customer-request-field">
                Số lượng yêu cầu <b>*</b>
                <input
                  v-model.number="formYeuCauDoiTra.SoLuongYeuCau"
                  type="number"
                  min="1"
                  :max="Number(requestModal.item?.SoLuong || 1)"
                />
                <small>Số lượng đã mua: {{ requestModal.item?.SoLuong || 0 }}. BE sẽ kiểm tra số lượng còn được phép yêu cầu.</small>
              </label>

              <div
                v-if="formYeuCauDoiTra.LoaiYeuCau === 'DoiHang'"
                class="customer-request-field"
              >
                Sản phẩm muốn đổi sang <b>*</b>
                <div class="product-picker">
                  <button
                    type="button"
                    class="product-picker-trigger"
                    :class="{ 'is-open': showProductDropdown }"
                    @click.stop="showProductDropdown = !showProductDropdown"
                  >
                    <span v-if="sanPhamDoiDaChon()" class="product-picker-selected">
                      <strong>{{ sanPhamDoiDaChon().TenSP }}</strong>
                      <small>
                        {{ formatMoney(sanPhamDoiDaChon().GiaBan) }}
                        · Còn {{ sanPhamDoiDaChon().SoLuongTon }}
                      </small>
                    </span>
                    <span v-else class="product-picker-placeholder">
                      <i class="fa-solid fa-box-open"></i>
                      Chọn sản phẩm bạn muốn đổi
                    </span>
                    <i
                      class="fa-solid fa-chevron-down product-picker-arrow"
                      :class="{ rotated: showProductDropdown }"
                    ></i>
                  </button>

                  <div
                    v-if="showProductDropdown"
                    class="product-picker-menu"
                    @click.stop
                  >
                    <div class="product-picker-search">
                      <i class="fa-solid fa-magnifying-glass"></i>
                      <input
                        v-model.trim="productSearch"
                        type="text"
                        placeholder="Tìm tên sản phẩm..."
                        @click.stop
                      />
                    </div>

                    <button
                      v-for="sp in sanPhamDoiSauTimKiem()"
                      :key="sp.MaSP"
                      type="button"
                      class="product-picker-option"
                      :class="{ selected: String(sp.MaSP) === String(formYeuCauDoiTra.MaSPDoi) }"
                      @click="chonSanPhamDoi(sp)"
                    >
                      <span class="product-picker-option-icon">
                        <i class="fa-solid fa-laptop"></i>
                      </span>
                      <span class="product-picker-option-info">
                        <strong>{{ sp.TenSP }}</strong>
                        <small>{{ formatMoney(sp.GiaBan) }}</small>
                      </span>
                      <span class="product-picker-stock">Còn {{ sp.SoLuongTon }}</span>
                    </button>

                    <div
                      v-if="sanPhamDoiSauTimKiem().length === 0"
                      class="product-picker-empty"
                    >
                      Không tìm thấy sản phẩm phù hợp.
                    </div>
                  </div>
                </div>
                <small>Chỉ hiển thị sản phẩm đang kinh doanh, còn hàng và khác sản phẩm đã mua.</small>
              </div>

              <label class="customer-request-field">
                Lý do đổi trả <b>*</b>
                <input
                  v-model.trim="formYeuCauDoiTra.LyDoYeuCau"
                  maxlength="255"
                  placeholder="VD: Sản phẩm bị lỗi, không đúng mô tả..."
                />
              </label>

              <label class="customer-request-field">
                Mô tả chi tiết
                <textarea
                  v-model.trim="formYeuCauDoiTra.MoTaChiTiet"
                  rows="4"
                  maxlength="5000"
                  placeholder="Mô tả thêm tình trạng và mong muốn xử lý..."
                ></textarea>
              </label>

            </template>

            <section class="proof-upload-section">
              <div class="proof-upload-heading">
                <div>
                  <strong>Ảnh minh chứng</strong>
                  <small>Chọn ảnh trực tiếp từ máy tính hoặc điện thoại</small>
                </div>
                <span class="proof-count">{{ selectedProofFiles.length }}/10</span>
              </div>

              <input
                ref="proofImageInput"
                class="proof-file-input"
                type="file"
                accept="image/jpeg,image/png,image/webp"
                multiple
                :disabled="submittingRequest"
                @change="chonAnhMinhChung"
              />

              <button
                type="button"
                class="proof-picker"
                :disabled="submittingRequest || selectedProofFiles.length >= 10"
                @click="moChonAnhMinhChung"
              >
                <span class="proof-picker-icon"><i class="fa-solid fa-cloud-arrow-up"></i></span>
                <span class="proof-picker-copy">
                  <strong>Nhấn để mở tệp và chọn ảnh</strong>
                  <small>JPG, PNG hoặc WEBP · Tối đa 5 MB mỗi ảnh</small>
                </span>
                <i class="fa-solid fa-plus proof-picker-plus"></i>
              </button>

              <div v-if="selectedProofPreviews.length" class="proof-preview-grid">
                <article
                  v-for="(preview, index) in selectedProofPreviews"
                  :key="preview.key"
                  class="proof-preview-card"
                >
                  <img :src="preview.url" :alt="preview.name" />
                  <button
                    type="button"
                    class="proof-remove-button"
                    :disabled="submittingRequest"
                    aria-label="Xóa ảnh"
                    @click="xoaAnhMinhChung(index)"
                  >
                    <i class="fa-solid fa-xmark"></i>
                  </button>
                  <span>{{ preview.name }}</span>
                </article>
              </div>
              <p class="proof-upload-hint">
                <i class="fa-solid fa-circle-info"></i>
                Ảnh sẽ được tải lên máy chủ khi bạn nhấn “Gửi yêu cầu”. Có thể chọn tối đa 10 ảnh.
              </p>
            </section>

            <div class="customer-request-modal-actions">
              <button
                type="button"
                class="secondary-button"
                :disabled="submittingRequest"
                @click="dongFormYeuCau"
              >
                Đóng
              </button>
              <button
                type="button"
                class="primary-button"
                :disabled="submittingRequest"
                @click="guiYeuCau"
              >
                <i class="fa-solid fa-paper-plane"></i>
                {{ submittingRequest ? 'Đang gửi...' : 'Gửi yêu cầu' }}
              </button>
            </div>
          </section>
        </div>

        <!-- MODAL PHẢN HỒI BÁO GIÁ / THÔNG BÁO THANH TOÁN -->
        <div
          v-if="customerActionModal.open"
          class="modal-backdrop-custom customer-response-backdrop"
          @click.self="dongModalKhachHang"
        >
          <section class="customer-response-modal" role="dialog" aria-modal="true">
            <header class="customer-response-header">
              <div>
                <span class="section-kicker">TECHSHOP CUSTOMER SERVICE</span>
                <h2>
                  {{ customerActionModal.mode === 'quote' ? 'Phản hồi báo giá bảo hành' : 'Thông báo thanh toán bảo hành' }}
                </h2>
                <p v-if="customerActionModal.request">
                  Yêu cầu #{{ customerActionModal.request.MaYCBH }}
                </p>
              </div>
              <button
                type="button"
                class="modal-close"
                :disabled="submittingCustomerAction"
                aria-label="Đóng"
                @click="dongModalKhachHang"
              >
                <i class="fa-solid fa-xmark"></i>
              </button>
            </header>

            <template v-if="customerActionModal.mode === 'quote' && customerActionModal.request">
              <div class="customer-response-intro">
                Cửa hàng đã kiểm tra sản phẩm và gửi báo giá. Bạn hãy xem chi phí trước khi quyết định.
              </div>
              <div class="customer-response-money">
                <div>
                  <span>Chi phí sửa chữa</span>
                  <strong>{{ formatMoney(customerActionModal.request.ChiPhiDuKien) }}</strong>
                </div>
                <div>
                  <span>Shop hỗ trợ ({{ Number(customerActionModal.request.TyLeShopHoTroApDung || 0) }}%)</span>
                  <strong>{{ formatMoney(customerActionModal.request.SoTienShopHoTro) }}</strong>
                </div>
                <div class="customer-response-total">
                  <span>Bạn cần thanh toán</span>
                  <strong>{{ formatMoney(customerActionModal.request.SoTienKhachTra) }}</strong>
                </div>
              </div>
              <p v-if="customerActionModal.request.GhiChuBaoGia" class="customer-response-note">
                <strong>Ghi chú của cửa hàng:</strong>
                {{ customerActionModal.request.GhiChuBaoGia }}
              </p>
              <p class="customer-response-warning">
                Khi đồng ý, yêu cầu sẽ chuyển sang chờ thanh toán (nếu có chi phí khách phải trả) hoặc chờ bảo hành miễn phí.
                Khi từ chối, cửa hàng sẽ sắp xếp gửi trả sản phẩm.
              </p>
              <div class="customer-response-actions">
  <button
    type="button"
    class="quote-reject-button"
    :disabled="submittingCustomerAction"
    @click="guiPhanHoiBaoGia('TuChoi')"
  >
    Từ chối báo giá
  </button>

  <button
    type="button"
    class="quote-accept-button"
    :disabled="submittingCustomerAction"
    @click="guiPhanHoiBaoGia('DongY')"
  >
    {{ submittingCustomerAction ? 'Đang gửi...' : 'Đồng ý báo giá' }}
  </button>
</div>
            </template>

            <template v-else-if="customerActionModal.mode === 'payment' && customerActionModal.request">
              <div class="customer-response-intro">
                Số tiền cần thanh toán:
                <strong>{{ formatMoney(customerActionModal.request.SoTienKhachTra) }}</strong>.
                Đây là bước thông báo cho cửa hàng biết bạn đã thanh toán; nhân viên sẽ kiểm tra và xác nhận.
              </div>
              <label class="customer-response-field">
                Phương thức thanh toán <b>*</b>
                <select v-model="customerActionModal.PhuongThucThanhToan">
                  <option value="ChuyenKhoan">Chuyển khoản</option>
                  <option value="TienMat">Tiền mặt tại cửa hàng</option>
                </select>
              </label>
              <label
                v-if="customerActionModal.PhuongThucThanhToan === 'ChuyenKhoan'"
                class="customer-response-field"
              >
                Mã giao dịch chuyển khoản <b>*</b>
                <input
                  v-model.trim="customerActionModal.MaGiaoDichThanhToan"
                  maxlength="100"
                  placeholder="Nhập mã giao dịch trên ứng dụng ngân hàng"
                />
              </label>
              <p class="customer-response-warning">
                Chỉ xác nhận sau khi bạn đã thực sự thanh toán. TECHSHOP sẽ kiểm tra thông tin trước khi tiếp tục bảo hành.
              </p>
              <div class="customer-response-actions">
  <button
    type="button"
    class="secondary-button"
    :disabled="submittingCustomerAction"
    @click="dongModalKhachHang"
  >
    Đóng
  </button>

  <button
    type="button"
    class="primary-button"
    :disabled="submittingCustomerAction"
    @click="guiThongBaoThanhToan"
  >
    {{ submittingCustomerAction ? 'Đang gửi...' : 'Tôi đã thanh toán' }}
  </button>
</div>
            </template>
          </section>
        </div>

      </teleport>

      <!-- FOOTER -->
      <footer class="history-footer">
        <strong>TECHSHOP</strong>
        <span>Đơn hàng minh bạch, theo dõi dễ dàng.</span>
      </footer>
    </div>
  </main>
</template>

<script>
import axios from "axios";

const API_ROOT = "http://127.0.0.1:8000";
const API = `${API_ROOT}/api`;

export default {
  name: "LichSuMuaHang",

  data() {
    return {
      orders: [],
      loading: false,
      error: "",
      keyword: "",
      activeFilter: "all",

      selectedOrder: null,
      orderToCancel: null,
      cancelingId: null,
      cancelReason: "",

      // Danh sách sản phẩm hiện tại.
      catalogProducts: [],
      catalogLoaded: false,
      catalogLoading: false,
      catalogLoadPromise: null,

      // Lịch sử yêu cầu bảo hành và đổi trả.
      warrantyRequests: [],
      returnRequests: [],
      requestLoadError: "",

      // Modal yêu cầu bảo hành / đổi trả.
      requestModal: {
        open: false,
        type: "",
        order: null,
        item: null,
      },

      submittingRequest: false,

      // Modal phản hồi báo giá / thông báo thanh toán bảo hành.
      customerActionModal: {
        open: false,
        mode: "",
        request: null,
        PhuongThucThanhToan: "ChuyenKhoan",
        MaGiaoDichThanhToan: "",
      },
      submittingCustomerAction: false,

      // Dropdown tìm kiếm sản phẩm đổi.
      showProductDropdown: false,
      productSearch: "",

      // Ảnh minh chứng chọn từ thiết bị.
      selectedProofFiles: [],
      selectedProofPreviews: [],
      proofPreviewSequence: 0,

      formYeuCauBaoHanh: {
        TinhTrangLoi: "",
      },

      formYeuCauDoiTra: {
        LoaiYeuCau: "TraHangHoanTien",
        SoLuongYeuCau: 1,
        MaSPDoi: "",
        LyDoYeuCau: "",
        MoTaChiTiet: "",
      },

      statusFilters: [
        { key: "all", label: "Tất cả" },
        { key: "ChoXacNhan", label: "Chờ xác nhận" },
        { key: "DaXacNhan", label: "Đã xác nhận" },
        { key: "DangGiao", label: "Đang giao" },
        { key: "DaGiao", label: "Đã giao" },
        { key: "DaHuy", label: "Đã hủy" },
      ],

      progressSteps: [
        { key: "ChoXacNhan", label: "Chờ xác nhận" },
        { key: "DaXacNhan", label: "Đã xác nhận" },
        { key: "DangGiao", label: "Đang giao" },
        { key: "DaGiao", label: "Đã giao" },
      ],
    };
  },

  computed: {
    filteredOrders() {
      const keyword = this.keyword.trim().toLocaleLowerCase("vi-VN");

      return this.orders.filter((order) => {
        const status = order.TrangThaiDH || "";

        if (
          this.activeFilter !== "all" &&
          status !== this.activeFilter
        ) {
          return false;
        }

        if (!keyword) return true;

        const productNames = this.getOrderItems(order)
          .map((item) => this.productName(item))
          .join(" ");

        const searchText = [
          order.MaDH,
          order.TenNguoiNhan,
          order.SoDienThoaiNhan,
          order.DiaChiGiaoHang,
          productNames,
        ]
          .filter(Boolean)
          .join(" ")
          .toLocaleLowerCase("vi-VN");

        return searchText.includes(keyword);
      });
    },
  },

  mounted() {
    this.loadOrders();
    this.loadCatalogProducts();
    this.loadCustomerRequests();
  },

  beforeUnmount() {
    // Thu hồi URL preview để tránh chiếm bộ nhớ.
    this.resetAnhMinhChung();
  },

  methods: {
    // =====================================================
    // XÁC THỰC VÀ CẤU HÌNH REQUEST
    // =====================================================

    getToken() {
      return (
        localStorage.getItem("token") ||
        localStorage.getItem("client_token") ||
        localStorage.getItem("user_token")
      );
    },

    requestConfig() {
      const token = this.getToken();

      return {
        headers: token
          ? { Authorization: `Bearer ${token}` }
          : {},
      };
    },

    // =====================================================
    // TẢI DANH SÁCH SẢN PHẨM HIỆN TẠI
    // =====================================================

    async loadCatalogProducts(force = false) {
      if (this.catalogLoadPromise) {
        return this.catalogLoadPromise;
      }

      if (this.catalogLoaded && !force) {
        return this.catalogProducts;
      }

      this.catalogLoading = true;

      this.catalogLoadPromise = (async () => {
        try {
          const response = await axios.get(
            `${API}/san-pham/get-data`
          );

          if (response.data?.status !== true) {
            throw new Error(
              response.data?.message ||
              "Không tải được danh sách sản phẩm hiện tại."
            );
          }

          this.catalogProducts = Array.isArray(response.data.data)
            ? response.data.data
            : [];

          this.catalogLoaded = true;

          return this.catalogProducts;
        } catch (error) {
          console.error(
            "Lỗi tải danh sách sản phẩm:",
            error.response?.data || error
          );

          this.catalogProducts = [];
          this.catalogLoaded = false;

          return [];
        } finally {
          this.catalogLoading = false;
          this.catalogLoadPromise = null;
        }
      })();

      return this.catalogLoadPromise;
    },

    async refreshAll() {
      await Promise.all([
        this.loadOrders(),
        this.loadCustomerRequests(),
        this.loadCatalogProducts(true),
      ]);
    },

    // =====================================================
    // TẢI LỊCH SỬ YÊU CẦU BẢO HÀNH / ĐỔI TRẢ
    // =====================================================

    async loadCustomerRequests() {
      if (!this.getToken()) return;

      this.requestLoadError = "";

      const config = this.requestConfig();

      const results = await Promise.allSettled([
        axios.get(`${API}/bao-hanh/get-data`, config),
        axios.get(`${API}/doi-tra/get-data`, config),
      ]);

      const warranty = results[0];
      const returns = results[1];

      if (
        warranty.status === "fulfilled" &&
        warranty.value.data?.status === true
      ) {
        this.warrantyRequests = Array.isArray(
          warranty.value.data.data
        )
          ? warranty.value.data.data
          : [];
      } else {
        this.requestLoadError =
          "Chưa tải được lịch sử yêu cầu bảo hành.";

        console.error(
          "Lỗi tải lịch sử bảo hành:",
          warranty.status === "rejected"
            ? warranty.reason
            : warranty.value.data
        );
      }

      if (
        returns.status === "fulfilled" &&
        returns.value.data?.status === true
      ) {
        this.returnRequests = Array.isArray(
          returns.value.data.data
        )
          ? returns.value.data.data
          : [];
      } else {
        this.requestLoadError = this.requestLoadError
          ? `${this.requestLoadError} Chưa tải được lịch sử đổi trả.`
          : "Chưa tải được lịch sử đổi trả.";

        console.error(
          "Lỗi tải lịch sử đổi trả:",
          returns.status === "rejected"
            ? returns.reason
            : returns.value.data
        );
      }
    },

    // =====================================================
    // ĐÁNH GIÁ SẢN PHẨM
    // =====================================================

    goToReview(order, item) {
      const maSP = Number(
        item?.MaSP ||
        item?.san_pham?.MaSP ||
        item?.sanPham?.MaSP
      );

      if (!maSP) {
        this.notify(
          "error",
          "Không xác định được sản phẩm cần đánh giá."
        );
        return;
      }

      this.$router.push({
        path: "/danh-gia",
        query: {
          MaSP: String(maSP),
          MaDH: String(order.MaDH),
        },
      });
    },

    // =====================================================
    // LẤY MÃ SẢN PHẨM VÀ CHI TIẾT ĐƠN HÀNG
    // =====================================================

    getMaSPFromOrderItem(item) {
      return Number(
        item?.MaSP ||
        item?.san_pham?.MaSP ||
        item?.sanPham?.MaSP ||
        0
      );
    },

    getMaCTDH(item) {
      return Number(item?.MaCTDH || 0);
    },

    // =====================================================
    // KIỂM TRA MUA LẠI
    // =====================================================

    kiemTraMuaLai(item) {
      const maSP = this.getMaSPFromOrderItem(item);

      if (!maSP) {
        return {
          duocMua: false,
          lyDo: "Không xác định được sản phẩm",
        };
      }

      if (!this.catalogLoaded) {
        return {
          duocMua: false,
          lyDo: this.catalogLoading
            ? "Đang kiểm tra sản phẩm..."
            : "Chưa kiểm tra được sản phẩm",
        };
      }

      const product = this.catalogProducts.find(
        (sp) => Number(sp.MaSP) === maSP
      );

      if (!product) {
        return {
          duocMua: false,
          lyDo: "Sản phẩm không còn tồn tại",
        };
      }

      if (product.TrangThaiSP !== "DangBan") {
        return {
          duocMua: false,
          lyDo: "Sản phẩm đã ngừng kinh doanh",
        };
      }

      if (
        product.SoLuongTon === null ||
        product.SoLuongTon === undefined ||
        product.SoLuongTon === ""
      ) {
        return {
          duocMua: false,
          lyDo: "Chưa xác định được tồn kho",
        };
      }

      if (Number(product.SoLuongTon) <= 0) {
        return {
          duocMua: false,
          lyDo: "Sản phẩm hiện đã hết hàng",
        };
      }

      return {
        duocMua: true,
        lyDo: "",
      };
    },

    async muaLai(order, item) {
      if (order?.TrangThaiDH !== "DaGiao") {
        this.notify(
          "error",
          "Chỉ đơn hàng đã giao mới hiển thị chức năng mua lại."
        );
        return;
      }

      await this.loadCatalogProducts(true);

      const result = this.kiemTraMuaLai(item);

      if (!result.duocMua) {
        this.notify("error", result.lyDo);
        return;
      }

      const maSP = this.getMaSPFromOrderItem(item);

      this.$router.push({
        path: "/chi-tiet-sp",
        query: {
          MaSP: String(maSP),
        },
      });
    },

    // =====================================================
    // CHÍNH SÁCH BẢO HÀNH
    // =====================================================

    kiemTraChinhSachBaoHanh(item, order = null) {
      const tong = item?.ThoiHanBaoHanhThang;
      const mienPhi = item?.ThoiHanMienPhiThang;
      const tyLe = item?.TyLeShopHoTro;

      if (
        tong === null ||
        tong === undefined ||
        tong === "" ||
        mienPhi === null ||
        mienPhi === undefined ||
        mienPhi === "" ||
        tyLe === null ||
        tyLe === undefined ||
        tyLe === ""
      ) {
        return {
          hopLe: false,
          hetHan: false,
        };
      }

      const total = Number(tong);
      const free = Number(mienPhi);
      const support = Number(tyLe);

      const hopLe =
        Number.isInteger(total) &&
        total > 0 &&
        Number.isInteger(free) &&
        free >= 0 &&
        free <= total &&
        Number.isFinite(support) &&
        support >= 0 &&
        support <= 100;

      if (!hopLe) {
        return {
          hopLe: false,
          hetHan: false,
        };
      }

      const deliveredAt =
        order?.NgayGiao ||
        item?.don_hang?.NgayGiao ||
        item?.donHang?.NgayGiao;

      if (!deliveredAt) {
        return {
          hopLe: true,
          hetHan: false,
        };
      }

      const expiry = this.tinhNgayHetHan(
        deliveredAt,
        total
      );

      return {
        hopLe: true,
        hetHan: expiry ? new Date() > expiry : false,
      };
    },

    tinhNgayHetHan(ngayGiao, soThang) {
      if (
        !ngayGiao ||
        !Number.isFinite(Number(soThang))
      ) {
        return null;
      }

      const date = new Date(
        String(ngayGiao).replace(" ", "T")
      );

      if (Number.isNaN(date.getTime())) {
        return null;
      }

      const originalDay = date.getDate();

      date.setDate(1);
      date.setMonth(
        date.getMonth() + Number(soThang)
      );

      const lastDay = new Date(
        date.getFullYear(),
        date.getMonth() + 1,
        0
      ).getDate();

      date.setDate(
        Math.min(originalDay, lastDay)
      );

      return date;
    },

    formatDateOnly(value) {
      if (!value) return "—";

      const date =
        value instanceof Date
          ? value
          : new Date(String(value).replace(" ", "T"));

      if (Number.isNaN(date.getTime())) {
        return "—";
      }

      return date.toLocaleDateString("vi-VN", {
        day: "2-digit",
        month: "2-digit",
        year: "numeric",
      });
    },

    // =====================================================
    // KIỂM TRA YÊU CẦU ĐANG XỬ LÝ
    // =====================================================

    timBaoHanhGanNhat(item) {
      const maCTDH = this.getMaCTDH(item);
      if (!maCTDH) return null;

      // API BE trả danh sách theo thời gian mới nhất trước.
      return this.warrantyRequests.find(
        (request) => Number(request.MaCTDH) === maCTDH
      ) || null;
    },

    timDoiTraGanNhat(item) {
      const maCTDH = this.getMaCTDH(item);
      if (!maCTDH) return null;

      return this.returnRequests.find(
        (request) => Number(request.MaCTDH) === maCTDH
      ) || null;
    },

    requestStatusClass(status) {
      if (["TuChoi", "DaHuy", "KhachTuChoiBaoGia"].includes(status)) {
        return "request-status-danger";
      }

      if ([
        "ChoXacNhan",
        "ChoKhachGuiHang",
        "ChoKhachXacNhanBaoGia",
        "ChoThanhToan",
        "ChoXacNhanThanhToan",
      ].includes(status)) {
        return "request-status-pending";
      }

      if ([
        "DaGiaoLaiKhach",
        "DaGiaoHangDoi",
        "DaHoanTien",
        "HoanTatBaoHanh",
        "HoanThanh",
      ].includes(status)) {
        return "request-status-success";
      }

      return "request-status-progress";
    },

    moTaTrangThaiYeuCau(type, request) {
      if (!request) return "";

      const warrantyMessages = {
        ChoXacNhan: "Yêu cầu đã được gửi đến TECHSHOP và đang chờ nhân viên tiếp nhận.",
        DaXacNhan: "TECHSHOP đã xác nhận yêu cầu. Vui lòng làm theo hướng dẫn gửi sản phẩm.",
        ChoKhachGuiHang: "Cửa hàng đang chờ bạn gửi sản phẩm về để kiểm tra.",
        DangVanChuyenVeShop: "Sản phẩm đang được vận chuyển về cửa hàng.",
        DaNhanHang: "Cửa hàng đã nhận sản phẩm và sẽ tiến hành kiểm tra.",
        DangKiemTra: "Kỹ thuật viên đang kiểm tra tình trạng sản phẩm.",
        ChoKhachXacNhanBaoGia: "Báo giá đã sẵn sàng. Bạn cần đồng ý hoặc từ chối trước khi cửa hàng tiếp tục.",
        ChoThanhToan: "Bạn đã đồng ý báo giá. Vui lòng thanh toán số tiền còn lại và thông báo cho cửa hàng.",
        ChoXacNhanThanhToan: "Cửa hàng đã nhận thông báo thanh toán và đang kiểm tra giao dịch.",
        DaThanhToan: "Thanh toán đã được cửa hàng xác nhận. Sản phẩm sẽ được đưa vào quy trình bảo hành.",
        ChoBaoHanh: "Cửa hàng đã tiếp nhận xử lý bảo hành miễn phí cho bạn.",
        DangBaoHanh: "Sản phẩm đang được kỹ thuật viên bảo hành.",
        HoanTatBaoHanh: "Sản phẩm đã được sửa chữa/bảo hành xong và đang chờ gửi lại.",
        DangVanChuyenTraKhach: "Sản phẩm đang được vận chuyển trả lại cho bạn.",
        DaGiaoLaiKhach: "Cửa hàng đã giao lại sản phẩm cho bạn.",
        KhachTuChoiBaoGia: "Bạn đã từ chối báo giá. Cửa hàng đang sắp xếp gửi trả sản phẩm.",
        TuChoi: "Cửa hàng đã từ chối yêu cầu bảo hành.",
        DaHuy: "Yêu cầu bảo hành đã được hủy.",
        HoanThanh: "Quy trình bảo hành đã hoàn tất.",
      };

      const returnMessages = {
        ChoXacNhan: "Yêu cầu đổi trả đã được gửi và đang chờ cửa hàng xác nhận.",
        DaXacNhan: "Cửa hàng đã xác nhận yêu cầu và sẽ hướng dẫn bước gửi sản phẩm.",
        ChoKhachGuiHang: "Cửa hàng đang chờ bạn gửi sản phẩm về.",
        DangVanChuyenVeShop: "Sản phẩm đang được vận chuyển về cửa hàng.",
        DaNhanHang: "Cửa hàng đã nhận sản phẩm đổi trả.",
        DangKiemTra: "Cửa hàng đang kiểm tra sản phẩm và tình trạng yêu cầu.",
        DangXuLy: "Yêu cầu đã được duyệt xử lý. Cửa hàng đang thực hiện đổi hàng hoặc hoàn tiền.",
        DangVanChuyenHangDoi: "Sản phẩm thay thế đang được vận chuyển đến bạn.",
        DaGiaoHangDoi: "Sản phẩm đổi đã được giao. Cửa hàng đang hoàn tất hồ sơ.",
        DaHoanTien: "Cửa hàng đã xác nhận hoàn tiền cho bạn.",
        DangVanChuyenTraKhach: "Sản phẩm đang được gửi trả lại cho bạn.",
        DaGiaoLaiKhach: "Sản phẩm đã được giao lại cho bạn.",
        HoanThanh: "Quy trình đổi trả đã hoàn tất.",
        TuChoi: "Cửa hàng đã từ chối yêu cầu đổi trả.",
        DaHuy: "Yêu cầu đổi trả đã được hủy.",
      };

      const messages = type === "bao-hanh" ? warrantyMessages : returnMessages;
      return messages[request.TrangThai] || "Cửa hàng đang cập nhật tiến độ xử lý yêu cầu.";
    },

    moModalPhanHoiBaoGia(request) {
      if (!request || request.TrangThai !== "ChoKhachXacNhanBaoGia") {
        this.notify("error", "Báo giá này hiện không còn chờ phản hồi.");
        return;
      }

      this.customerActionModal = {
        open: true,
        mode: "quote",
        request,
        PhuongThucThanhToan: "ChuyenKhoan",
        MaGiaoDichThanhToan: "",
      };
    },

    moModalThongBaoThanhToan(request) {
      if (!request || request.TrangThai !== "ChoThanhToan") {
        this.notify("error", "Yêu cầu này hiện không chờ thanh toán.");
        return;
      }

      this.customerActionModal = {
        open: true,
        mode: "payment",
        request,
        PhuongThucThanhToan: "ChuyenKhoan",
        MaGiaoDichThanhToan: "",
      };
    },

    dongModalKhachHang(force = false) {
      if (this.submittingCustomerAction && !force) return;

      this.customerActionModal = {
        open: false,
        mode: "",
        request: null,
        PhuongThucThanhToan: "ChuyenKhoan",
        MaGiaoDichThanhToan: "",
      };
    },

    async guiPhanHoiBaoGia(phanHoi) {
      const request = this.customerActionModal.request;

      if (
        this.submittingCustomerAction ||
        this.customerActionModal.mode !== "quote" ||
        !request ||
        !["DongY", "TuChoi"].includes(phanHoi)
      ) {
        return;
      }

      this.submittingCustomerAction = true;

      try {
        const response = await axios.put(
          `${API}/bao-hanh/phan-hoi-bao-gia`,
          {
            MaYCBH: request.MaYCBH,
            PhanHoi: phanHoi,
          },
          this.requestConfig()
        );

        if (response?.data?.status !== true) {
          throw new Error(response?.data?.message || "Không gửi được phản hồi báo giá.");
        }

        const message = response.data.message || "Đã ghi nhận phản hồi báo giá.";
        this.dongModalKhachHang(true);
        await this.loadCustomerRequests();
        this.notify("success", message);
      } catch (error) {
        const errors = error.response?.data?.errors;
        const validation = errors ? Object.values(errors).flat().join(" ") : "";
        this.notify(
          "error",
          validation || error.response?.data?.message || error.message || "Không gửi được phản hồi báo giá."
        );
      } finally {
        this.submittingCustomerAction = false;
      }
    },

    async guiThongBaoThanhToan() {
      const request = this.customerActionModal.request;
      const method = this.customerActionModal.PhuongThucThanhToan;
      const transactionCode = this.customerActionModal.MaGiaoDichThanhToan.trim();

      if (
        this.submittingCustomerAction ||
        this.customerActionModal.mode !== "payment" ||
        !request
      ) {
        return;
      }

      if (method === "ChuyenKhoan" && !transactionCode) {
        this.notify("error", "Vui lòng nhập mã giao dịch chuyển khoản.");
        return;
      }

      this.submittingCustomerAction = true;

      try {
        const response = await axios.put(
          `${API}/bao-hanh/thong-bao-thanh-toan`,
          {
            MaYCBH: request.MaYCBH,
            PhuongThucThanhToan: method,
            MaGiaoDichThanhToan: method === "ChuyenKhoan" ? transactionCode : null,
          },
          this.requestConfig()
        );

        if (response?.data?.status !== true) {
          throw new Error(response?.data?.message || "Không gửi được thông báo thanh toán.");
        }

        const message = response.data.message || "Đã gửi thông báo thanh toán.";
        this.dongModalKhachHang(true);
        await this.loadCustomerRequests();
        this.notify("success", message);
      } catch (error) {
        const errors = error.response?.data?.errors;
        const validation = errors ? Object.values(errors).flat().join(" ") : "";
        this.notify(
          "error",
          validation || error.response?.data?.message || error.message || "Không gửi được thông báo thanh toán."
        );
      } finally {
        this.submittingCustomerAction = false;
      }
    },

    timBaoHanhDangXuLy(item) {
      const maCTDH = this.getMaCTDH(item);

      if (!maCTDH) return null;

      const terminal = [
        "TuChoi",
        "DaHuy",
        "HoanThanh",
      ];

      return (
        this.warrantyRequests.find(
          (request) =>
            Number(request.MaCTDH) === maCTDH &&
            !terminal.includes(request.TrangThai)
        ) || null
      );
    },

    timDoiTraDangXuLy(item) {
      const maCTDH = this.getMaCTDH(item);

      if (!maCTDH) return null;

      const terminal = [
        "TuChoi",
        "DaHuy",
        "DaGiaoLaiKhach",
        "HoanThanh",
        "DaHoanTien",
        "DaGiaoHangDoi",
      ];

      return (
        this.returnRequests.find(
          (request) =>
            Number(request.MaCTDH) === maCTDH &&
            !terminal.includes(request.TrangThai)
        ) || null
      );
    },

    tenTrangThaiYeuCau(status) {
      const labels = {
        ChoXacNhan: "Chờ xác nhận",
        DaXacNhan: "Đã xác nhận",
        ChoKhachGuiHang: "Chờ khách gửi hàng",
        DangVanChuyenVeShop: "Đang vận chuyển về shop",
        DaNhanHang: "Shop đã nhận hàng",
        DangKiemTra: "Đang kiểm tra",
        ChoKhachXacNhanBaoGia: "Chờ xác nhận báo giá",
        ChoThanhToan: "Chờ thanh toán",
        ChoXacNhanThanhToan: "Chờ xác nhận thanh toán",
        DaThanhToan: "Đã thanh toán",
        ChoBaoHanh: "Chờ bảo hành",
        DangBaoHanh: "Đang bảo hành",
        HoanTatBaoHanh: "Đã hoàn tất bảo hành",
        DangVanChuyenTraKhach: "Đang gửi trả khách",
        DaGiaoLaiKhach: "Đã giao lại khách",
        KhachTuChoiBaoGia: "Khách từ chối báo giá",
        DangXuLy: "Đang xử lý",
        DangVanChuyenHangDoi: "Đang giao hàng đổi",
        DaGiaoHangDoi: "Đã giao hàng đổi",
        DaHoanTien: "Đã hoàn tiền",
        HoanThanh: "Hoàn thành",
        TuChoi: "Từ chối",
        DaHuy: "Đã hủy",
      };

      return labels[status] || status || "Chưa xác định";
    },

    // =====================================================
    // MỞ FORM BẢO HÀNH
    // =====================================================

    moFormBaoHanh(order, item) {
      if (order?.TrangThaiDH !== "DaGiao") {
        this.notify(
          "error",
          "Chỉ đơn hàng đã giao mới được yêu cầu bảo hành."
        );
        return;
      }

      const policy = this.kiemTraChinhSachBaoHanh(
        item,
        order
      );

      if (!policy.hopLe) {
        this.notify(
          "error",
          "Đơn hàng chưa lưu đầy đủ chính sách bảo hành tại thời điểm mua."
        );
        return;
      }

      if (policy.hetHan) {
        this.notify(
          "error",
          "Sản phẩm đã hết thời hạn bảo hành."
        );
        return;
      }

      if (this.timBaoHanhDangXuLy(item)) {
        this.notify(
          "error",
          "Sản phẩm đang có yêu cầu bảo hành được xử lý."
        );
        return;
      }

      this.resetAnhMinhChung();

      this.showProductDropdown = false;
      this.productSearch = "";

      this.formYeuCauBaoHanh = {
        TinhTrangLoi: "",
      };

      this.requestModal = {
        open: true,
        type: "bao-hanh",
        order,
        item,
      };
    },

    // =====================================================
    // MỞ FORM ĐỔI TRẢ
    // =====================================================

    async moFormDoiTra(order, item) {
      if (order?.TrangThaiDH !== "DaGiao") {
        this.notify(
          "error",
          "Chỉ đơn hàng đã giao mới được yêu cầu đổi trả."
        );
        return;
      }

      if (this.timDoiTraDangXuLy(item)) {
        this.notify(
          "error",
          "Sản phẩm đang có yêu cầu đổi trả được xử lý."
        );
        return;
      }

      await this.loadCatalogProducts(true);

      this.formYeuCauDoiTra = {
        LoaiYeuCau: "TraHangHoanTien",
        SoLuongYeuCau: 1,
        MaSPDoi: "",
        LyDoYeuCau: "",
        MoTaChiTiet: "",
      };

      this.resetAnhMinhChung();

      this.showProductDropdown = false;
      this.productSearch = "";

      this.requestModal = {
        open: true,
        type: "doi-tra",
        order,
        item,
      };
    },

    // =====================================================
    // DROPDOWN CHỌN SẢN PHẨM ĐỔI
    // =====================================================

    sanPhamCoTheDoi(item) {
      const currentId = this.getMaSPFromOrderItem(item);

      return this.catalogProducts.filter((product) => {
        return (
          Number(product.MaSP) !== currentId &&
          product.TrangThaiSP === "DangBan" &&
          Number(product.SoLuongTon) > 0
        );
      });
    },

    sanPhamDoiDaChon() {
      return (
        this.sanPhamCoTheDoi(
          this.requestModal.item
        ).find(
          (product) =>
            String(product.MaSP) ===
            String(this.formYeuCauDoiTra.MaSPDoi)
        ) || null
      );
    },

    sanPhamDoiSauTimKiem() {
      const keyword = this.productSearch
        .trim()
        .toLocaleLowerCase("vi-VN");

      return this.sanPhamCoTheDoi(
        this.requestModal.item
      ).filter((product) =>
        String(product.TenSP || "")
          .toLocaleLowerCase("vi-VN")
          .includes(keyword)
      );
    },

    chonSanPhamDoi(product) {
      this.formYeuCauDoiTra.MaSPDoi = String(
        product.MaSP
      );

      this.showProductDropdown = false;
      this.productSearch = "";
    },

    // =====================================================
    // ĐÓNG FORM YÊU CẦU
    // =====================================================

    dongFormYeuCau() {
      if (this.submittingRequest) return;

      this.resetAnhMinhChung();

      this.showProductDropdown = false;
      this.productSearch = "";

      this.requestModal = {
        open: false,
        type: "",
        order: null,
        item: null,
      };
    },

    // =====================================================
    // CHỌN ẢNH TỪ MÁY TÍNH / ĐIỆN THOẠI
    // =====================================================

    moChonAnhMinhChung() {
      if (
        this.submittingRequest ||
        this.selectedProofFiles.length >= 10
      ) {
        return;
      }

      this.$refs.proofImageInput?.click();
    },

    chonAnhMinhChung(event) {
      const picked = Array.from(
        event?.target?.files || []
      );

      if (event?.target) {
        event.target.value = "";
      }

      if (!picked.length) return;

      const remaining = Math.max(
        0,
        10 - this.selectedProofFiles.length
      );

      const accepted = [];
      const rejected = [];

      picked.forEach((file) => {
        const validType = [
          "image/jpeg",
          "image/png",
          "image/webp",
        ].includes(file.type);

        const validSize =
          file.size <= 5 * 1024 * 1024;

        if (!validType || !validSize) {
          rejected.push(file.name);
          return;
        }

        if (accepted.length < remaining) {
          accepted.push(file);
        }
      });

      if (rejected.length) {
        this.notify(
          "error",
          "Ảnh phải là JPG, PNG hoặc WEBP và không quá 5 MB mỗi ảnh."
        );
      }

      if (picked.length - rejected.length > remaining) {
        this.notify(
          "error",
          "Mỗi yêu cầu chỉ được chọn tối đa 10 ảnh."
        );
      }

      accepted.forEach((file) => {
        const key = ++this.proofPreviewSequence;

        this.selectedProofFiles.push(file);

        this.selectedProofPreviews.push({
          key,
          name: file.name,
          url: URL.createObjectURL(file),
        });
      });
    },

    xoaAnhMinhChung(index) {
      const preview =
        this.selectedProofPreviews[index];

      if (preview?.url) {
        URL.revokeObjectURL(preview.url);
      }

      this.selectedProofFiles.splice(index, 1);
      this.selectedProofPreviews.splice(index, 1);
    },

    resetAnhMinhChung() {
      this.selectedProofPreviews.forEach(
        (preview) => {
          if (preview?.url) {
            URL.revokeObjectURL(preview.url);
          }
        }
      );

      this.selectedProofFiles = [];
      this.selectedProofPreviews = [];

      if (this.$refs.proofImageInput) {
        this.$refs.proofImageInput.value = "";
      }
    },

    // =====================================================
    // UPLOAD ẢNH LÊN LARAVEL
    // =====================================================

    async taiAnhMinhChungLenMayChu() {
      if (!this.selectedProofFiles.length) {
        return [];
      }

      const formData = new FormData();

      this.selectedProofFiles.forEach((file) => {
        formData.append("files[]", file, file.name);
      });

      const response = await axios.post(
        `${API}/upload/minh-chung`,
        formData,
        this.requestConfig()
      );

      if (response?.data?.status !== true) {
        throw new Error(
          response?.data?.message ||
          "Không tải được ảnh minh chứng lên máy chủ."
        );
      }

      const urls =
        response?.data?.data?.urls ||
        response?.data?.urls ||
        [];

      if (
        !Array.isArray(urls) ||
        urls.length !== this.selectedProofFiles.length
      ) {
        throw new Error(
          "Máy chủ chưa trả về đủ đường dẫn ảnh đã tải lên."
        );
      }

      return urls;
    },

    // =====================================================
    // GỬI YÊU CẦU BẢO HÀNH / ĐỔI TRẢ
    // =====================================================

    async guiYeuCau() {
      if (this.submittingRequest) return;

      const {
        order,
        item,
        type,
      } = this.requestModal;

      if (
        !order ||
        !item ||
        order.TrangThaiDH !== "DaGiao"
      ) {
        this.notify(
          "error",
          "Chỉ đơn hàng đã giao mới được gửi yêu cầu."
        );
        return;
      }

      const maCTDH = this.getMaCTDH(item);

      if (!maCTDH) {
        this.notify(
          "error",
          "Không xác định được mã chi tiết đơn hàng (MaCTDH)."
        );
        return;
      }

      this.submittingRequest = true;

      try {
        let response;

        // ---------------------------------------------
        // GỬI YÊU CẦU BẢO HÀNH
        // ---------------------------------------------

        if (type === "bao-hanh") {
          const tinhTrangLoi =
            this.formYeuCauBaoHanh.TinhTrangLoi.trim();

          if (!tinhTrangLoi) {
            this.notify(
              "error",
              "Vui lòng mô tả tình trạng lỗi sản phẩm."
            );
            return;
          }

          if (this.selectedProofFiles.length > 10) {
            this.notify(
              "error",
              "Tối đa 10 ảnh minh chứng."
            );
            return;
          }

          const images =
            await this.taiAnhMinhChungLenMayChu();

          response = await axios.post(
            `${API}/bao-hanh/add-data`,
            {
              MaCTDH: maCTDH,
              TinhTrangLoi: tinhTrangLoi,
              HinhAnhMinhChung: images,
            },
            this.requestConfig()
          );
        }

        // ---------------------------------------------
        // GỬI YÊU CẦU ĐỔI TRẢ
        // ---------------------------------------------

        else if (type === "doi-tra") {
          const form = this.formYeuCauDoiTra;

          const quantity = Number(
            form.SoLuongYeuCau
          );

          const purchasedQuantity = Number(
            item.SoLuong || 0
          );

          if (!form.LyDoYeuCau.trim()) {
            this.notify(
              "error",
              "Vui lòng nhập lý do đổi trả."
            );
            return;
          }

          if (
            !Number.isInteger(quantity) ||
            quantity < 1 ||
            quantity > purchasedQuantity
          ) {
            this.notify(
              "error",
              "Số lượng yêu cầu không hợp lệ."
            );
            return;
          }

          if (this.selectedProofFiles.length > 10) {
            this.notify(
              "error",
              "Tối đa 10 ảnh minh chứng."
            );
            return;
          }

          let maSPDoi = null;

          if (form.LoaiYeuCau === "DoiHang") {
            maSPDoi = Number(form.MaSPDoi);

            const selectedProduct =
              this.catalogProducts.find(
                (sp) =>
                  Number(sp.MaSP) === maSPDoi
              );

            if (
              !maSPDoi ||
              !selectedProduct ||
              selectedProduct.TrangThaiSP !== "DangBan" ||
              Number(selectedProduct.SoLuongTon) <= 0
            ) {
              this.notify(
                "error",
                "Vui lòng chọn sản phẩm đổi còn kinh doanh và còn hàng."
              );
              return;
            }
          }

          const images =
            await this.taiAnhMinhChungLenMayChu();

          response = await axios.post(
            `${API}/doi-tra/add-data`,
            {
              MaCTDH: maCTDH,
              LoaiYeuCau: form.LoaiYeuCau,
              SoLuongYeuCau: quantity,
              MaSPDoi: maSPDoi,
              LyDoYeuCau: form.LyDoYeuCau.trim(),
              MoTaChiTiet:
                form.MoTaChiTiet.trim() || null,
              HinhAnhMinhChung: images,
            },
            this.requestConfig()
          );
        } else {
          throw new Error(
            "Loại yêu cầu không hợp lệ."
          );
        }

        if (response?.data?.status !== true) {
          throw new Error(
            response?.data?.message ||
            "Gửi yêu cầu thất bại."
          );
        }

        const message =
          response.data.message ||
          "Gửi yêu cầu thành công.";

        const quaHan =
          response.data.CanhBaoQuaHan === true;

        this.requestModal = {
          open: false,
          type: "",
          order: null,
          item: null,
        };

        this.resetAnhMinhChung();

        this.showProductDropdown = false;
        this.productSearch = "";

        await Promise.all([
          this.loadCustomerRequests(),
          this.loadOrders(),
        ]);

        this.notify(
          "success",
          quaHan
            ? `${message} Cửa hàng sẽ xem xét trường hợp quá hạn.`
            : message
        );
      } catch (error) {
        console.error(
          "Lỗi gửi yêu cầu:",
          error.response?.data || error
        );

        const errors =
          error.response?.data?.errors;

        const validation = errors
          ? Object.values(errors).flat().join(" ")
          : "";

        this.notify(
          "error",
          validation ||
          error.response?.data?.message ||
          error.message ||
          "Không thể gửi yêu cầu."
        );
      } finally {
        this.submittingRequest = false;
      }
    },

    // =====================================================
    // TẢI DANH SÁCH ĐƠN HÀNG
    // =====================================================

    async loadOrders() {
      if (this.loading) return;

      const token = this.getToken();

      if (!token) {
        this.orders = [];
        this.error =
          "Bạn cần đăng nhập để xem lịch sử mua hàng.";
        return;
      }

      this.loading = true;
      this.error = "";

      try {
        const response = await axios.get(
          `${API}/don-hang/get-data`,
          this.requestConfig()
        );

        if (response.data?.status !== true) {
          throw new Error(
            response.data?.message ||
            "Không thể tải danh sách đơn hàng."
          );
        }

        this.orders = Array.isArray(response.data.data)
          ? response.data.data
          : [];

        window.dispatchEvent(
          new Event("techshop:orders-updated")
        );
      } catch (error) {
        console.error(
          "Lỗi tải lịch sử đơn hàng:",
          error
        );

        this.error =
          error.response?.data?.message ||
          error.message ||
          "Không thể tải lịch sử mua hàng.";

        if (error.response?.status === 401) {
          this.error =
            "Phiên đăng nhập đã hết hạn. Vui lòng đăng nhập lại.";
        }
      } finally {
        this.loading = false;
      }
    },

    // =====================================================
    // LỌC VÀ THỐNG KÊ ĐƠN HÀNG
    // =====================================================

    countStatus(status) {
      return this.orders.filter(
        (order) => order.TrangThaiDH === status
      ).length;
    },

    countFilter(filter) {
      if (filter === "all") {
        return this.orders.length;
      }

      return this.countStatus(filter);
    },

    resetFilters() {
      this.keyword = "";
      this.activeFilter = "all";
    },

    // =====================================================
    // THÔNG TIN TRẠNG THÁI
    // =====================================================

    statusInfo(status) {
      const map = {
        ChoXacNhan: {
          label: "Chờ xác nhận",
          className: "status-pending",
          icon: "fa-regular fa-clock",
          description:
            "TECHSHOP đã nhận đơn và đang chờ cửa hàng xác nhận.",
        },

        DaXacNhan: {
          label: "Đã xác nhận",
          className: "status-confirmed",
          icon: "fa-solid fa-circle-check",
          description:
            "Cửa hàng đã xác nhận đơn hàng của bạn.",
        },

        DangGiao: {
          label: "Đang giao",
          className: "status-shipping",
          icon: "fa-solid fa-truck-fast",
          description:
            "Đơn hàng đang trong quá trình giao đến địa chỉ nhận.",
        },

        DaGiao: {
          label: "Đã giao",
          className: "status-delivered",
          icon: "fa-solid fa-circle-check",
          description:
            "Đơn hàng đã được cập nhật là giao thành công.",
        },

        DaHuy: {
          label: "Đã hủy",
          className: "status-cancelled",
          icon: "fa-solid fa-circle-xmark",
          description:
            "Đơn hàng đã hủy. Nếu hủy thành công, BE đã xử lý hoàn tồn kho.",
        },
      };

      return (
        map[status] || {
          label: status || "Chưa xác định",
          className: "status-unknown",
          icon: "fa-solid fa-circle-info",
          description:
            "Trạng thái đơn hàng chưa được xác định.",
        }
      );
    },

    // =====================================================
    // TIẾN TRÌNH ĐƠN HÀNG
    // =====================================================

    progressStepClass(order, stepKey) {
      const statuses = this.progressSteps.map(
        (step) => step.key
      );

      const currentIndex = statuses.indexOf(
        order.TrangThaiDH
      );

      const stepIndex = statuses.indexOf(stepKey);

      if (currentIndex === -1) return "";

      if (stepIndex < currentIndex) {
        return "step-done";
      }

      if (stepIndex === currentIndex) {
        return "step-current";
      }

      return "";
    },

    isStepCompleted(order, stepKey) {
      const statuses = this.progressSteps.map(
        (step) => step.key
      );

      const currentIndex = statuses.indexOf(
        order.TrangThaiDH
      );

      const stepIndex = statuses.indexOf(stepKey);

      return (
        currentIndex !== -1 &&
        stepIndex < currentIndex
      );
    },

    // =====================================================
    // LẤY CHI TIẾT SẢN PHẨM TRONG ĐƠN
    // =====================================================

    getOrderItems(order) {
      const items =
        order?.chi_tiet_don_hangs ||
        order?.chiTietDonHangs ||
        order?.chi_tiet_don_hang ||
        [];

      return Array.isArray(items) ? items : [];
    },

    productName(item) {
      const product =
        item?.san_pham ||
        item?.sanPham ||
        {};

      return (
        product.TenSP ||
        item.TenSP ||
        `Sản phẩm #${item.MaSP || ""}`
      );
    },

    productImage(item) {
      const product =
        item?.san_pham ||
        item?.sanPham ||
        {};

      return (
        product.HinhAnh ||
        item.HinhAnh ||
        ""
      );
    },

    totalUnits(order) {
      return this.getOrderItems(order).reduce(
        (sum, item) =>
          sum + Number(item.SoLuong || 0),
        0
      );
    },

    // =====================================================
    // ĐỊNH DẠNG TIỀN, NGÀY VÀ ẢNH
    // =====================================================

    formatMoney(value) {
      return `${Number(value || 0).toLocaleString("vi-VN")} ₫`;
    },

    formatDate(value) {
      if (!value) return "Chưa có ngày đặt";

      const date = new Date(
        String(value).replace(" ", "T")
      );

      if (Number.isNaN(date.getTime())) {
        return String(value);
      }

      return date.toLocaleString("vi-VN", {
        day: "2-digit",
        month: "2-digit",
        year: "numeric",
        hour: "2-digit",
        minute: "2-digit",
      });
    },

    imageUrl(path) {
      if (!path) return "";

      const value = String(path);

      if (/^https?:\/\//i.test(value)) {
        return value;
      }

      if (
        value.startsWith("/images/") ||
        value.startsWith("/img_") ||
        value.startsWith("/assets/")
      ) {
        return value;
      }

      if (value.startsWith("/storage/")) {
        return `${API_ROOT}${value}`;
      }

      return `${API_ROOT}/storage/${value
        .replace(/^\/?storage\//, "")
        .replace(/^\/+/, "")}`;
    },

    hideBrokenImage(event) {
      event.target.style.display = "none";
    },

    // =====================================================
    // XEM CHI TIẾT ĐƠN HÀNG
    // =====================================================

    openDetails(order) {
      this.selectedOrder = order;
    },

    // =====================================================
    // HỦY ĐƠN HÀNG
    // =====================================================

    canCancel(order) {
      return [
        "ChoXacNhan",
        "DaXacNhan",
      ].includes(order?.TrangThaiDH);
    },

    askCancel(order) {
      if (!this.canCancel(order)) {
        this.notify(
          "error",
          "Đơn hàng không thể hủy ở trạng thái hiện tại."
        );
        return;
      }

      this.cancelReason = "";
      this.orderToCancel = order;
    },

    async confirmCancel() {
      const order = this.orderToCancel;
      const reason = this.cancelReason.trim();

      if (
        !order ||
        !this.canCancel(order) ||
        this.cancelingId
      ) {
        return;
      }

      if (!reason) {
        this.notify(
          "error",
          "Vui lòng nhập lý do hủy đơn hàng."
        );
        return;
      }

      this.cancelingId = String(order.MaDH);
      this.error = "";

      try {
        const response = await axios.delete(
          `${API}/don-hang/delete`,
          {
            ...this.requestConfig(),
            data: {
              id: order.MaDH,
              LyDoHuy: reason,
            },
          }
        );

        if (response.data?.status !== true) {
          throw new Error(
            response.data?.message ||
            "Không thể hủy đơn hàng."
          );
        }

        this.orderToCancel = null;
        this.selectedOrder = null;
        this.cancelReason = "";

        await this.loadOrders();

        window.dispatchEvent(
          new Event("techshop:orders-updated")
        );

        this.notify(
          "success",
          "Hủy đơn hàng thành công."
        );
      } catch (error) {
        console.error(
          "Lỗi hủy đơn:",
          error
        );

        const message =
          error.response?.data?.message ||
          (
            error.response?.data?.errors &&
            Object.values(
              error.response.data.errors
            ).flat().join(" ")
          ) ||
          error.message ||
          "Không thể hủy đơn hàng.";

        this.notify("error", message);
      } finally {
        this.cancelingId = null;
      }
    },

    // =====================================================
    // THÔNG BÁO
    // =====================================================

    notify(type, message) {
      if (
        this.$toast &&
        typeof this.$toast[type] === "function"
      ) {
        this.$toast[type](message);
      } else {
        window.alert(message);
      }
    },
  },
};
</script>
<style scoped>
/* =========================================================
   BIẾN MÀU - dùng chung cho trang và các modal teleport ra body
   ========================================================= */
.history-page,
.modal-backdrop-custom {
  --ink: #0f172a;
  --text: #334155;
  --muted: #64748b;
  --line: #e2e8f0;
  --soft: #f1f5f9;
  --primary: #4f46e5;
  --blue: #2563eb;
  --blue-dark: #1d4ed8;
  --accent: #06b6d4;
  --grad-primary: linear-gradient(135deg, #4f46e5 0%, #2563eb 55%, #06b6d4 100%);
  --grad-hero: linear-gradient(135deg, #1e1b4b 0%, #312e81 45%, #1d4ed8 100%);
  --grad-danger: linear-gradient(135deg, #f43f5e 0%, #e11d48 100%);
  --grad-success: linear-gradient(135deg, #34d399 0%, #059669 100%);
  --shadow-sm: 0 2px 10px rgba(15, 23, 42, 0.06);
  --shadow-md: 0 10px 30px rgba(15, 23, 42, 0.08);
  --shadow-lg: 0 24px 70px rgba(15, 23, 42, 0.22);
  --ease: cubic-bezier(0.22, 1, 0.36, 1);
}

/* =========================================================
   KEYFRAMES
   ========================================================= */
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(18px); }
  to { opacity: 1; transform: translateY(0); }
}
@keyframes spin {
  to { transform: rotate(360deg); }
}
@keyframes floatBlob {
  0%, 100% { transform: translate(0, 0) scale(1); }
  50% { transform: translate(24px, -18px) scale(1.08); }
}
@keyframes pulseRing {
  0% { box-shadow: 0 0 0 0 rgba(79, 70, 229, 0.45); }
  70% { box-shadow: 0 0 0 9px rgba(79, 70, 229, 0); }
  100% { box-shadow: 0 0 0 0 rgba(79, 70, 229, 0); }
}
@keyframes shine {
  from { transform: translateX(-120%) skewX(-20deg); }
  to { transform: translateX(260%) skewX(-20deg); }
}
@keyframes modalIn {
  from { opacity: 0; transform: translateY(18px) scale(0.97); }
  to { opacity: 1; transform: translateY(0) scale(1); }
}
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* =========================================================
   TRANG
   ========================================================= */
.history-page {
  position: relative;
  isolation: isolate;
  min-height: 100vh;
  overflow: hidden;
  padding: 36px 24px 30px;
  color: var(--ink);
  background:
    radial-gradient(1000px 500px at 100% -10%, rgba(99, 102, 241, 0.12), transparent 60%),
    radial-gradient(800px 500px at -10% 30%, rgba(6, 182, 212, 0.1), transparent 60%),
    #f4f6fc;
  font-family: "Roboto", "Inter", Arial, sans-serif;
}

.history-glow {
  position: absolute;
  z-index: -1;
  width: 440px;
  height: 440px;
  border-radius: 50%;
  filter: blur(90px);
  opacity: 0.35;
  pointer-events: none;
  animation: floatBlob 14s ease-in-out infinite;
}
.glow-left { top: -160px; left: -140px; background: #a5b4fc; }
.glow-right { top: 320px; right: -200px; background: #67e8f9; animation-delay: -6s; }

.history-shell {
  width: min(1160px, 100%);
  margin: 0 auto;
}

/* =========================================================
   HEADER (banner gradient)
   ========================================================= */
.history-header {
  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 24px;
  margin-bottom: 24px;
  padding: 34px 36px;
  border-radius: 26px;
  color: #fff;
  background: var(--grad-hero);
  box-shadow: 0 22px 50px rgba(49, 46, 129, 0.35);
  animation: fadeUp 0.6s var(--ease) both;
}
.history-header::before,
.history-header::after {
  content: "";
  position: absolute;
  border-radius: 50%;
  pointer-events: none;
}
.history-header::before {
  top: -90px;
  right: -60px;
  width: 300px;
  height: 300px;
  background: radial-gradient(circle, rgba(34, 211, 238, 0.45), transparent 68%);
}
.history-header::after {
  bottom: -120px;
  left: 30%;
  width: 280px;
  height: 280px;
  background: radial-gradient(circle, rgba(167, 139, 250, 0.4), transparent 68%);
}
.history-header > * { position: relative; z-index: 1; }

.history-kicker,
.section-kicker {
  display: flex;
  align-items: center;
  gap: 9px;
  color: #818cf8;
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 1.8px;
}
.history-kicker { color: #c7d2fe; }

.kicker-line {
  width: 28px;
  height: 3px;
  border-radius: 5px;
  background: linear-gradient(90deg, #22d3ee, #a78bfa);
}

.history-header h1 {
  margin: 14px 0 10px;
  color: #fff;
  font-size: clamp(30px, 4.4vw, 46px);
  font-weight: 900;
  letter-spacing: -1.6px;
  line-height: 1.1;
}
.history-header h1 span {
  background: linear-gradient(90deg, #22d3ee, #a78bfa);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.history-header p {
  max-width: 560px;
  margin: 0;
  color: #c7d2fe;
  font-size: 14px;
  line-height: 1.75;
}

/* =========================================================
   NÚT
   ========================================================= */
.refresh-button,
.primary-button,
.secondary-button,
.cancel-button,
.cancel-confirm-button,
.quote-reject-button,
.quote-accept-button {
  position: relative;
  overflow: hidden;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 9px;
  min-height: 44px;
  padding: 0 18px;
  border-radius: 12px;
  font-size: 12.5px;
  font-weight: 800;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease),
    background 0.25s, border-color 0.25s, color 0.25s;
}

.refresh-button {
  border: 1px solid rgba(255, 255, 255, 0.28);
  color: #fff;
  background: rgba(255, 255, 255, 0.14);
  backdrop-filter: blur(10px);
}
.refresh-button:hover:not(:disabled) {
  background: rgba(255, 255, 255, 0.26);
  transform: translateY(-2px);
}

.primary-button,
.quote-accept-button {
  border: 0;
  color: #fff;
  background: var(--grad-primary);
  background-size: 140% 140%;
  background-position: 0% 50%;
  box-shadow: 0 8px 20px rgba(79, 70, 229, 0.32);
}
.primary-button::after {
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
.primary-button:hover:not(:disabled),
.quote-accept-button:hover:not(:disabled) {
  transform: translateY(-2px);
  background-position: 100% 50%;
  box-shadow: 0 14px 28px rgba(79, 70, 229, 0.42);
}
.primary-button:hover:not(:disabled)::after { animation: shine 0.8s var(--ease); }
.primary-button:active:not(:disabled) { transform: scale(0.97); }

.secondary-button,
.quote-reject-button {
  border: 1.5px solid var(--line);
  color: #475569;
  background: #fff;
}
.secondary-button:hover:not(:disabled),
.quote-reject-button:hover:not(:disabled) {
  color: var(--primary);
  border-color: #c7d2fe;
  background: #eef2ff;
  transform: translateY(-2px);
}

.cancel-button {
  border: 1.5px solid #fecdd3;
  color: #be123c;
  background: #fff;
}
.cancel-button:hover:not(:disabled) {
  border-color: #fda4af;
  background: #fff1f2;
  transform: translateY(-2px);
}

.cancel-confirm-button {
  border: 0;
  color: #fff;
  background: var(--grad-danger);
  box-shadow: 0 8px 20px rgba(244, 63, 94, 0.3);
}
.cancel-confirm-button:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 14px 28px rgba(244, 63, 94, 0.42);
}

.refresh-button:disabled,
.primary-button:disabled,
.secondary-button:disabled,
.cancel-button:disabled,
.cancel-confirm-button:disabled,
.quote-reject-button:disabled,
.quote-accept-button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}

.spinning { animation: spin 0.9s linear infinite; }

/* =========================================================
   THỐNG KÊ
   ========================================================= */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 16px;
  margin-bottom: 24px;
}

.stat-card {
  --tone: #4f46e5;
  --tone-soft: rgba(79, 70, 229, 0.1);
  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
  gap: 15px;
  min-width: 0;
  padding: 20px 18px;
  border: 1px solid rgba(226, 232, 240, 0.9);
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.92);
  box-shadow: var(--shadow-sm);
  animation: fadeUp 0.55s var(--ease) both;
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease), border-color 0.3s;
}
.stat-card:nth-child(1) { --tone: #4f46e5; --tone-soft: rgba(79, 70, 229, 0.1); animation-delay: 0.08s; }
.stat-card:nth-child(2) { --tone: #f59e0b; --tone-soft: rgba(245, 158, 11, 0.13); animation-delay: 0.14s; }
.stat-card:nth-child(3) { --tone: #7c3aed; --tone-soft: rgba(124, 58, 237, 0.11); animation-delay: 0.2s; }
.stat-card:nth-child(4) { --tone: #10b981; --tone-soft: rgba(16, 185, 129, 0.12); animation-delay: 0.26s; }

.stat-card::before {
  content: "";
  position: absolute;
  top: -40px;
  right: -40px;
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
.stat-card:hover {
  transform: translateY(-6px);
  border-color: transparent;
  box-shadow: 0 18px 38px rgba(15, 23, 42, 0.12);
}
.stat-card:hover::before { transform: scale(1.5); }
.stat-card:hover::after { transform: scaleX(1); }

.stat-icon {
  position: relative;
  z-index: 1;
  display: grid;
  place-items: center;
  flex: 0 0 52px;
  width: 52px;
  height: 52px;
  border-radius: 16px;
  color: #fff;
  font-size: 20px;
  transition: transform 0.35s var(--ease);
}
.stat-card:hover .stat-icon { transform: rotate(-8deg) scale(1.1); }

.icon-total { background: linear-gradient(135deg, #6366f1, #4f46e5); box-shadow: 0 8px 16px rgba(79, 70, 229, 0.35); }
.icon-pending { background: linear-gradient(135deg, #fbbf24, #f97316); box-shadow: 0 8px 16px rgba(245, 158, 11, 0.38); }
.icon-shipping { background: linear-gradient(135deg, #a78bfa, #7c3aed); box-shadow: 0 8px 16px rgba(124, 58, 237, 0.35); }
.icon-complete { background: linear-gradient(135deg, #34d399, #059669); box-shadow: 0 8px 16px rgba(16, 185, 129, 0.35); }

.stat-card > div:last-child {
  position: relative;
  z-index: 1;
  min-width: 0;
}
.stat-card span,
.stat-card small {
  display: block;
  color: var(--muted);
  font-size: 12px;
  font-weight: 600;
}
.stat-card strong {
  display: block;
  margin: 5px 0;
  color: var(--ink);
  font-size: 30px;
  font-weight: 900;
  line-height: 1;
}
.stat-card small {
  overflow: hidden;
  color: #94a3b8;
  font-size: 11px;
  font-weight: 400;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* =========================================================
   THANH CÔNG CỤ: TÌM KIẾM + LỌC
   ========================================================= */
.orders-toolbar {
  margin-bottom: 20px;
  padding: 24px;
  border: 1px solid var(--line);
  border-radius: 22px;
  background: #fff;
  box-shadow: var(--shadow-md);
  animation: fadeUp 0.6s var(--ease) 0.3s both;
}
.toolbar-heading {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 15px;
  margin-bottom: 18px;
}
.toolbar-heading h2 {
  margin: 8px 0 0;
  color: var(--ink);
  font-size: 23px;
  font-weight: 900;
  letter-spacing: -0.6px;
}
.result-count {
  padding: 8px 14px;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
  font-size: 12px;
  font-weight: 700;
  white-space: nowrap;
}

.search-orders {
  display: flex;
  align-items: center;
  gap: 11px;
  min-height: 50px;
  padding: 0 15px;
  border: 1.5px solid var(--line);
  border-radius: 14px;
  color: #94a3b8;
  background: #f8fafc;
  transition: all 0.25s var(--ease);
}
.search-orders:hover { border-color: #c7d2fe; }
.search-orders:focus-within {
  border-color: var(--primary);
  color: var(--primary);
  background: #fff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}
.search-orders input {
  flex: 1;
  width: 0;
  border: 0;
  outline: none;
  color: #1e293b;
  background: transparent;
  font: inherit;
  font-size: 13px;
}
.search-orders input::placeholder { color: #a0aabc; }
.search-orders button {
  display: grid;
  place-items: center;
  width: 28px;
  height: 28px;
  border: 0;
  border-radius: 8px;
  color: #64748b;
  background: transparent;
  cursor: pointer;
  transition: 0.2s;
}
.search-orders button:hover { color: #e11d48; background: #fff1f2; }

.filter-tabs {
  display: flex;
  gap: 8px;
  margin-top: 18px;
  padding-bottom: 4px;
  overflow-x: auto;
}
.filter-tabs::-webkit-scrollbar { height: 6px; }
.filter-tabs::-webkit-scrollbar-thumb { border-radius: 6px; background: #c7d2fe; }

.filter-tab {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 9px;
  min-height: 40px;
  padding: 0 15px;
  border: 1.5px solid var(--line);
  border-radius: 12px;
  color: var(--muted);
  background: #fff;
  font-size: 12px;
  font-weight: 700;
  white-space: nowrap;
  cursor: pointer;
  transition: all 0.28s var(--ease);
}
.filter-tab span {
  display: grid;
  place-items: center;
  min-width: 22px;
  height: 22px;
  padding: 0 6px;
  border-radius: 999px;
  color: inherit;
  background: rgba(100, 116, 139, 0.14);
  font-size: 10.5px;
  font-weight: 800;
}
.filter-tab:hover:not(.active) {
  color: var(--primary);
  border-color: #c7d2fe;
  background: #eef2ff;
  transform: translateY(-2px);
}
.filter-tab.active {
  border-color: transparent;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.35);
}
.filter-tab.active span { background: rgba(255, 255, 255, 0.25); }

/* =========================================================
   CẢNH BÁO / TRẠNG THÁI RỖNG / LOADING
   ========================================================= */
.history-alert {
  display: flex;
  align-items: center;
  gap: 11px;
  margin-bottom: 18px;
  padding: 14px 18px;
  border: 1px solid #fecdd3;
  border-radius: 14px;
  color: #be123c;
  background: linear-gradient(180deg, #fff1f2, #fff);
  font-size: 12.5px;
  font-weight: 600;
  animation: fadeUp 0.4s var(--ease) both;
}
.history-alert span { flex: 1; }
.history-alert button {
  border: 0;
  color: inherit;
  background: transparent;
  cursor: pointer;
}
.request-load-alert {
  border-color: #fde68a;
  color: #92400e;
  background: linear-gradient(180deg, #fffbeb, #fff);
}
.request-retry-button {
  padding: 7px 13px;
  border: 0;
  border-radius: 9px;
  color: #fff !important;
  background: var(--grad-primary) !important;
  font-size: 11px;
  font-weight: 800;
  cursor: pointer;
}

.history-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 320px;
  padding: 44px 20px;
  border: 1px solid var(--line);
  border-radius: 22px;
  text-align: center;
  background: #fff;
  box-shadow: var(--shadow-md);
  animation: fadeUp 0.5s var(--ease) both;
}
.state-spinner {
  width: 44px;
  height: 44px;
  border: 4px solid #e0e7ff;
  border-top-color: var(--primary);
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}
.state-icon {
  display: grid;
  place-items: center;
  width: 82px;
  height: 82px;
  margin-bottom: 14px;
  border-radius: 50%;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 32px;
}
.state-error { color: #e11d48; background: #ffe4e6; }
.history-state h3 {
  margin: 16px 0 8px;
  color: var(--ink);
  font-size: 19px;
  font-weight: 900;
}
.history-state p {
  max-width: 430px;
  margin: 0 0 20px;
  color: var(--muted);
  font-size: 13px;
  line-height: 1.75;
}

/* =========================================================
   THẺ ĐƠN HÀNG
   ========================================================= */
.orders-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.order-card {
  position: relative;
  overflow: hidden;
  border: 1px solid var(--line);
  border-radius: 22px;
  background: #fff;
  box-shadow: var(--shadow-sm);
  animation: fadeUp 0.55s var(--ease) both;
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease), border-color 0.3s;
}
.order-card::before {
  content: "";
  position: absolute;
  inset: 0 0 auto 0;
  height: 4px;
  background: var(--grad-primary);
  opacity: 0.85;
}
.order-card:hover {
  transform: translateY(-4px);
  border-color: #c7d2fe;
  box-shadow: 0 20px 44px rgba(49, 46, 129, 0.13);
}

.order-card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 18px;
  padding: 24px 24px 18px;
  background: linear-gradient(180deg, #f5f7ff 0%, #fff 100%);
  border-bottom: 1px solid #eef2f7;
}
.order-id-block {
  display: flex;
  flex-direction: column;
  gap: 4px;
}
.order-id-label {
  color: #94a3b8;
  font-size: 10px;
  font-weight: 800;
  letter-spacing: 1.5px;
}
.order-id-block strong {
  color: var(--ink);
  font-size: 21px;
  font-weight: 900;
  letter-spacing: -0.4px;
}
.order-id-block small {
  color: var(--muted);
  font-size: 11.5px;
}

.status-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 9px 14px;
  border-radius: 999px;
  font-size: 11.5px;
  font-weight: 800;
  white-space: nowrap;
}
.status-pending { color: #b45309; background: #fef3c7; }
.status-confirmed { color: #3730a3; background: #e0e7ff; }
.status-shipping { color: #6d28d9; background: #ede9fe; }
.status-delivered { color: #047857; background: #d1fae5; }
.status-cancelled { color: #be123c; background: #ffe4e6; }
.status-unknown { color: #475569; background: #e2e8f0; }

/* =========================================================
   TIẾN TRÌNH
   ========================================================= */
.order-progress {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  padding: 24px 24px 22px;
  border-bottom: 1px solid #eef2f7;
}
.progress-step {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 9px;
  text-align: center;
}
.progress-step:not(:last-child)::after {
  content: "";
  position: absolute;
  top: 13px;
  left: calc(50% + 18px);
  width: calc(100% - 36px);
  height: 3px;
  border-radius: 3px;
  background: #e2e8f0;
}
.progress-step.step-done:not(:last-child)::after {
  background: linear-gradient(90deg, #10b981, #34d399);
}
.progress-marker {
  position: relative;
  z-index: 1;
  display: grid;
  place-items: center;
  width: 28px;
  height: 28px;
  border: 2.5px solid #e2e8f0;
  border-radius: 50%;
  color: #94a3b8;
  background: #fff;
  font-size: 10px;
}
.progress-step small {
  color: #94a3b8;
  font-size: 11px;
  font-weight: 700;
}
.progress-step.step-done .progress-marker {
  border-color: transparent;
  color: #fff;
  background: var(--grad-success);
  box-shadow: 0 4px 10px rgba(16, 185, 129, 0.35);
}
.progress-step.step-done small { color: #047857; }
.progress-step.step-current .progress-marker {
  border-color: transparent;
  color: #fff;
  background: var(--grad-primary);
  animation: pulseRing 1.8s ease-out infinite;
}
.progress-step.step-current small { color: var(--primary); font-weight: 900; }

.cancelled-banner {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 18px 24px;
  border-bottom: 1px solid #ffe4e6;
  color: #be123c;
  background: linear-gradient(90deg, #fff1f2, #fff);
}
.cancelled-banner > i { font-size: 24px; }
.cancelled-banner strong,
.cancelled-banner span,
.cancelled-banner small { display: block; }
.cancelled-banner strong { margin-bottom: 4px; font-size: 13px; }
.cancelled-banner span { color: #9f1239; font-size: 11.5px; line-height: 1.6; }
.cancelled-banner small { margin-top: 3px; color: #be7a85; font-size: 10.5px; }

/* =========================================================
   SẢN PHẨM TRONG ĐƠN
   ========================================================= */
.order-products { padding: 8px 24px; }

.order-product {
  display: flex;
  align-items: flex-start;
  gap: 15px;
  padding: 18px 0;
  border-bottom: 1px dashed #e2e8f0;
}
.order-product:last-child { border-bottom: 0; }

.order-product-image,
.detail-product-image {
  display: grid;
  place-items: center;
  flex-shrink: 0;
  width: 78px;
  height: 78px;
  overflow: hidden;
  border: 2px solid #fff;
  border-radius: 18px;
  background: #f8fafc;
  box-shadow: 0 0 0 2px #e0e7ff, 0 6px 14px rgba(79, 70, 229, 0.12);
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease);
}
.order-product:hover .order-product-image {
  transform: scale(1.06) rotate(-2deg);
  box-shadow: 0 0 0 2px #818cf8, 0 10px 20px rgba(79, 70, 229, 0.25);
}
.order-product-image img,
.detail-product-image img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}
.product-image-placeholder {
  display: grid;
  place-items: center;
  width: 100%;
  height: 100%;
  color: #a5b4fc;
  font-size: 24px;
}

.order-product-info {
  display: flex;
  flex: 1;
  flex-direction: column;
  gap: 7px;
  min-width: 0;
}
.order-product-info > strong {
  color: var(--ink);
  font-size: 14px;
  font-weight: 800;
  line-height: 1.5;
}
.order-product-info > span {
  display: inline-flex;
  width: fit-content;
  padding: 3px 10px;
  border-radius: 999px;
  color: var(--muted);
  background: var(--soft);
  font-size: 11px;
  font-weight: 700;
}
.order-product-price {
  color: var(--ink);
  font-size: 15px;
  font-weight: 900;
  white-space: nowrap;
}

.more-products {
  margin: 2px 0 14px;
  padding: 9px 13px;
  border-radius: 10px;
  color: var(--primary);
  background: #eef2ff;
  font-size: 11.5px;
  font-weight: 700;
}
.missing-details {
  padding: 16px 0;
  color: #b45309;
  font-size: 12px;
}

/* ---- Nút trong sản phẩm ---- */
.review-product-button {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  width: fit-content;
  padding: 8px 13px;
  border: 1px solid #fde68a;
  border-radius: 10px;
  color: #92400e;
  background: linear-gradient(135deg, #fffbeb, #fef3c7);
  font-size: 11.5px;
  font-weight: 800;
  cursor: pointer;
  transition: 0.25s var(--ease);
}
.review-product-button i { color: #f59e0b; }
.review-product-button:hover {
  transform: translateY(-2px);
  border-color: #fbbf24;
  box-shadow: 0 8px 16px rgba(245, 158, 11, 0.25);
}

.order-item-actions,
.customer-request-actions {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 8px;
  margin-top: 6px;
}

.repurchase-button,
.customer-action-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  min-height: 36px;
  padding: 0 13px;
  border: 1px solid transparent;
  border-radius: 10px;
  font-size: 11.5px;
  font-weight: 800;
  cursor: pointer;
  transition: 0.25s var(--ease);
}
.warranty-request-button {
  color: #1d4ed8;
  border-color: #bfdbfe;
  background: linear-gradient(135deg, #eff6ff, #dbeafe);
}
.return-request-button {
  color: #6d28d9;
  border-color: #ddd6fe;
  background: linear-gradient(135deg, #f5f3ff, #ede9fe);
}
.repurchase-button {
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
}
.customer-action-button:hover,
.repurchase-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 10px 20px rgba(79, 70, 229, 0.25);
}
.repurchase-unavailable {
  color: #b45309;
  font-size: 11px;
  font-weight: 700;
}
.request-status-inline {
  display: inline-flex;
  align-items: center;
  padding: 7px 11px;
  border-radius: 9px;
  color: #475569;
  background: var(--soft);
  font-size: 11px;
  font-weight: 700;
}
.request-status-muted { color: #92400e; background: #fef3c7; }

/* =========================================================
   FOOTER THẺ + HÀNH ĐỘNG
   ========================================================= */
.order-card-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 15px;
  padding: 18px 24px;
  border-top: 1px solid #eef2f7;
  background: #f8fafc;
}
.order-summary-left { display: flex; flex-direction: column; gap: 4px; }
.order-summary-left span { color: #475569; font-size: 12px; font-weight: 800; }
.order-summary-left small { color: #94a3b8; font-size: 11px; }

.order-total { display: flex; align-items: center; gap: 14px; }
.order-total span { color: var(--muted); font-size: 12px; font-weight: 600; }
.order-total strong {
  font-size: 24px;
  font-weight: 900;
  letter-spacing: -0.6px;
  white-space: nowrap;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.order-actions {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  padding: 0 24px 22px;
  background: #f8fafc;
}
.order-actions button { min-height: 42px; padding: 0 16px; font-size: 12px; }

/* =========================================================
   THEO DÕI BẢO HÀNH / ĐỔI TRẢ
   ========================================================= */
.request-tracking-card {
  width: 100%;
  margin: 8px 0;
  padding: 15px 16px;
  border: 1px solid #e2e8f0;
  border-left: 4px solid #3b82f6;
  border-radius: 14px;
  background: linear-gradient(180deg, #fbfcff, #fff);
  box-shadow: var(--shadow-sm);
}
.warranty-tracking-card { border-left-color: #4f46e5; }
.return-tracking-card { border-left-color: #10b981; }

.request-tracking-heading {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 10px;
}
.request-tracking-kicker {
  display: block;
  margin-bottom: 5px;
  color: #64748b;
  font-size: 10px;
  font-weight: 900;
  letter-spacing: 1.2px;
}
.request-status-pill {
  display: inline-flex;
  flex: 0 0 auto;
  align-items: center;
  gap: 6px;
  padding: 6px 11px;
  border-radius: 999px;
  font-size: 10.5px;
  font-weight: 800;
}
.request-status-pill::before {
  content: "";
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: currentColor;
}
.request-status-pending { color: #b45309; background: #fef3c7; }
.request-status-progress { color: #3730a3; background: #e0e7ff; }
.request-status-success { color: #047857; background: #d1fae5; }
.request-status-danger { color: #be123c; background: #ffe4e6; }

.request-tracking-description {
  margin: 9px 0 6px;
  color: #475569;
  font-size: 12.5px;
  line-height: 1.65;
}
.request-tracking-date { color: #94a3b8; font-size: 11px; }

.request-quote-summary,
.request-refund-summary {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 10px;
  margin-top: 11px;
  padding: 13px;
  border: 1px solid #e0e7ff;
  border-radius: 12px;
  background: linear-gradient(180deg, #eef2ff, #f8faff);
}
.request-quote-summary > div,
.request-refund-summary > div { display: flex; flex-direction: column; gap: 4px; }
.request-quote-summary span,
.request-refund-summary span { color: #64748b; font-size: 11px; }
.request-quote-summary strong,
.request-refund-summary strong { color: #1e293b; font-size: 13px; font-weight: 800; }
.request-quote-summary .request-customer-amount {
  grid-column: 1 / -1;
  padding-top: 10px;
  border-top: 1px dashed #c7d2fe;
}
.request-quote-summary .request-customer-amount strong {
  color: var(--primary);
  font-size: 19px;
  font-weight: 900;
}
.request-refund-summary small { grid-column: 1 / -1; color: #64748b; font-size: 11px; }

.request-tracking-note {
  margin: 9px 0 0;
  padding: 9px 12px;
  border-radius: 10px;
  color: #475569;
  background: #f1f5f9;
  font-size: 11.5px;
  line-height: 1.6;
}
.request-rejection-note { color: #be123c; background: #fff1f2; }
.request-waiting-note {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-top: 10px;
  padding: 9px 12px;
  border-radius: 10px;
  color: #475569;
  background: #f1f5f9;
  font-size: 11.5px;
}
.request-exchange-product { margin-top: 9px; color: #475569; font-size: 12px; }
.request-exchange-product strong { color: var(--ink); }
.request-customer-actions { display: flex; flex-wrap: wrap; gap: 8px; margin-top: 11px; }

/* =========================================================
   MODAL CHUNG
   ========================================================= */
.modal-backdrop-custom {
  position: fixed;
  inset: 0;
  z-index: 30000;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  overflow-y: auto;
  background: rgba(15, 23, 42, 0.6);
  backdrop-filter: blur(8px) saturate(125%);
  animation: fadeIn 0.25s ease both;
}
.request-modal-backdrop { z-index: 30002; padding: 14px; }
/* sửa lỗi cũ: modal phản hồi báo giá phải nằm TRÊN modal chi tiết đơn */
.customer-response-backdrop { z-index: 30004; }

.order-modal,
.cancel-modal,
.customer-request-modal,
.customer-response-modal {
  position: relative;
  z-index: 30001;
  flex-shrink: 0;
  margin: auto;
  border: 1px solid rgba(255, 255, 255, 0.75);
  background: #fff;
  box-shadow: var(--shadow-lg);
  animation: modalIn 0.32s var(--ease) both;
}

.order-modal {
  width: min(720px, 100%);
  max-height: min(92vh, 920px);
  overflow: auto;
  border-radius: 26px;
}

.modal-header {
  position: relative;
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 18px;
  padding: 26px 28px;
  color: #fff;
  background: var(--grad-hero);
}
.modal-header::after {
  content: "";
  position: absolute;
  top: -70px;
  right: -40px;
  width: 220px;
  height: 220px;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(34, 211, 238, 0.4), transparent 68%);
  pointer-events: none;
}
.modal-header > * { position: relative; z-index: 1; }
.modal-header .section-kicker { color: #c7d2fe; }
.modal-header h2 {
  margin: 8px 0 5px;
  color: #fff;
  font-size: 25px;
  font-weight: 900;
  letter-spacing: -0.5px;
}
.modal-header p { margin: 0; color: #c7d2fe; font-size: 12px; }

.modal-close {
  display: grid;
  place-items: center;
  flex: 0 0 38px;
  width: 38px;
  height: 38px;
  border: 1px solid #e2e8f0;
  border-radius: 12px;
  color: #475569;
  background: #fff;
  cursor: pointer;
  transition: all 0.25s var(--ease);
}
.modal-header .modal-close {
  border-color: rgba(255, 255, 255, 0.3);
  color: #fff;
  background: rgba(255, 255, 255, 0.15);
}
.modal-close:hover:not(:disabled) {
  color: #fff;
  border-color: transparent;
  background: var(--grad-danger);
  transform: rotate(90deg);
}

.modal-content { padding: 24px 28px; }

.detail-status-panel {
  padding: 16px;
  border: 1px solid #e0e7ff;
  border-radius: 16px;
  background: linear-gradient(180deg, #eef2ff, #f8faff);
}
.detail-status-panel p {
  margin: 11px 0 0;
  color: #475569;
  font-size: 12.5px;
  line-height: 1.7;
}

.detail-section { margin-top: 26px; }
.detail-section h3 {
  display: flex;
  align-items: center;
  gap: 10px;
  margin: 0 0 15px;
  color: var(--ink);
  font-size: 14.5px;
  font-weight: 900;
}
.detail-section h3 i {
  display: grid;
  place-items: center;
  width: 30px;
  height: 30px;
  border-radius: 9px;
  color: #fff;
  background: var(--grad-primary);
  font-size: 12px;
}

.detail-info-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 12px;
}
.detail-info-grid > div {
  display: flex;
  flex-direction: column;
  gap: 6px;
  padding: 14px;
  border: 1px solid var(--line);
  border-radius: 14px;
  background: #f8fafc;
}
.detail-info-grid .detail-full { grid-column: 1 / -1; }
.detail-info-grid span,
.detail-money-line span { color: var(--muted); font-size: 11px; font-weight: 600; }
.detail-info-grid strong {
  color: var(--ink);
  font-size: 12.5px;
  line-height: 1.7;
  overflow-wrap: anywhere;
}

.detail-product {
  display: flex;
  align-items: flex-start;
  gap: 14px;
  padding: 16px 0;
  border-bottom: 1px dashed #e2e8f0;
}
.detail-product:last-child { border-bottom: 0; }
.detail-product-image { width: 66px; height: 66px; border-radius: 16px; }
.detail-product-copy {
  display: flex;
  flex: 1;
  flex-direction: column;
  gap: 6px;
  min-width: 0;
}
.detail-product-copy strong { color: var(--ink); font-size: 13px; line-height: 1.5; }
.detail-product-copy span,
.detail-product-copy small { color: var(--muted); font-size: 11.5px; }
.detail-product > strong { color: var(--ink); font-size: 13px; font-weight: 900; white-space: nowrap; }

.detail-money-line,
.detail-total-line {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 14px;
  padding: 13px 0;
  border-bottom: 1px solid #f1f5f9;
}
.detail-money-line strong {
  max-width: 65%;
  color: #334155;
  font-size: 12px;
  text-align: right;
  overflow-wrap: anywhere;
}
.detail-total-line {
  align-items: center;
  margin-top: 6px;
  padding: 16px;
  border: 0;
  border-radius: 14px;
  background: linear-gradient(135deg, #eef2ff, #e0f2fe);
}
.detail-total-line span { color: #334155; font-size: 13px; font-weight: 800; }
.detail-total-line strong {
  font-size: 24px;
  font-weight: 900;
  white-space: nowrap;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.modal-footer {
  position: sticky;
  bottom: 0;
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  padding: 16px 28px;
  border-top: 1px solid #eef2f7;
  background: rgba(255, 255, 255, 0.92);
  backdrop-filter: blur(12px);
}

/* ---- Bảo hành snapshot ---- */
.warranty-snapshot {
  width: 100%;
  margin-top: 8px;
  padding: 14px;
  border: 1px solid #c7d2fe;
  border-radius: 16px;
  background: linear-gradient(180deg, #eef2ff, #f8faff);
}
.warranty-snapshot-heading {
  display: flex;
  align-items: center;
  gap: 9px;
  margin-bottom: 11px;
  color: var(--primary);
  font-size: 13px;
  font-weight: 900;
}
.warranty-snapshot-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 9px;
}
.warranty-snapshot-grid > div {
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding: 10px 11px;
  border: 1px solid #e0e7ff;
  border-radius: 11px;
  background: #fff;
}
.warranty-snapshot-grid span { color: var(--muted); font-size: 10.5px; }
.warranty-snapshot-grid strong { color: var(--ink); font-size: 12.5px; font-weight: 800; }
.warranty-snapshot-grid .warranty-expiry { grid-column: 1 / -1; }
.warranty-note { margin: 10px 0 0; color: #64748b; font-size: 10.5px; line-height: 1.6; }
.warranty-policy-missing {
  display: flex;
  gap: 8px;
  color: #b45309;
  font-size: 11.5px;
  line-height: 1.6;
}

/* =========================================================
   MODAL HỦY ĐƠN
   ========================================================= */
.cancel-modal {
  width: min(440px, 100%);
  padding: 30px;
  border-radius: 24px;
  text-align: center;
}
.cancel-modal-icon {
  display: grid;
  place-items: center;
  width: 70px;
  height: 70px;
  margin: 0 auto 18px;
  border-radius: 50%;
  color: #fff;
  background: var(--grad-danger);
  box-shadow: 0 12px 26px rgba(244, 63, 94, 0.4);
  font-size: 26px;
  animation: pulseRing 2s ease-out infinite;
}
.cancel-modal h2 { margin: 0 0 10px; color: var(--ink); font-size: 23px; font-weight: 900; }
.cancel-modal p { margin: 0; color: var(--muted); font-size: 13px; line-height: 1.8; }
.cancel-modal p strong { color: var(--ink); }
.cancel-modal-actions { display: flex; justify-content: center; gap: 10px; margin-top: 24px; }

.cancel-reason-field {
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin: 20px 0;
  text-align: left;
}
.cancel-reason-field label { color: #1e293b; font-size: 13px; font-weight: 800; }
.cancel-reason-field label span { color: #e11d48; }
.cancel-reason-field textarea {
  width: 100%;
  min-height: 92px;
  padding: 12px 14px;
  border: 1.5px solid var(--line);
  border-radius: 12px;
  outline: none;
  background: #f8fafc;
  font: inherit;
  font-size: 13px;
  resize: vertical;
  transition: all 0.25s var(--ease);
}
.cancel-reason-field textarea:focus {
  border-color: var(--primary);
  background: #fff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}
.cancel-reason-field textarea:disabled { background: #f1f5f9; }
.cancel-reason-field small { align-self: flex-end; color: #94a3b8; font-size: 11.5px; }

/* =========================================================
   MODAL YÊU CẦU BẢO HÀNH / ĐỔI TRẢ
   ========================================================= */
.customer-request-modal {
  width: min(640px, 100%);
  max-height: min(92dvh, 920px);
  padding: 28px;
  overflow-y: auto;
  border-radius: 26px;
  background: linear-gradient(180deg, #fff 0%, #fafbff 100%);
}
.customer-request-modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 14px;
  margin: -2px 0 20px;
  padding-bottom: 18px;
  border-bottom: 1px solid #eef2f7;
}
.customer-request-modal-header .section-kicker { font-size: 10px; letter-spacing: 1.5px; }
.customer-request-modal-header h2 {
  margin: 8px 0 6px;
  font-size: clamp(22px, 3vw, 26px);
  font-weight: 900;
  letter-spacing: -0.6px;
  background: linear-gradient(90deg, #0f172a 0%, #4f46e5 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.customer-request-modal-header p { margin: 0; color: var(--muted); font-size: 12px; line-height: 1.6; }

.request-info-note {
  display: flex;
  align-items: flex-start;
  gap: 11px;
  margin-bottom: 20px;
  padding: 14px 16px;
  border: 1px solid #c7d2fe;
  border-radius: 14px;
  color: #3730a3;
  background: linear-gradient(180deg, #eef2ff, #f8faff);
  font-size: 12px;
  line-height: 1.7;
}
.request-info-note > i { margin-top: 2px; font-size: 16px; }

.customer-request-field {
  display: block;
  margin: 16px 0;
  color: #334155;
  font-size: 12.5px;
  font-weight: 800;
}
.customer-request-field b { color: #e11d48; }
.customer-request-field input,
.customer-request-field select,
.customer-request-field textarea {
  display: block;
  width: 100%;
  margin-top: 8px;
  padding: 12px 14px;
  border: 1.5px solid var(--line);
  border-radius: 12px;
  outline: none;
  color: #1e293b;
  background: #f8fafc;
  font-family: inherit;
  font-size: 13px;
  line-height: 1.6;
  transition: all 0.25s var(--ease);
}
.customer-request-field textarea { min-height: 112px; resize: vertical; }
.customer-request-field input:hover,
.customer-request-field select:hover,
.customer-request-field textarea:hover { border-color: #c7d2fe; }
.customer-request-field input:focus,
.customer-request-field select:focus,
.customer-request-field textarea:focus {
  border-color: var(--primary);
  background: #fff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}
.customer-request-field small {
  display: block;
  margin-top: 6px;
  color: #94a3b8;
  font-size: 11px;
  font-weight: 400;
  line-height: 1.6;
}

.customer-request-modal-actions {
  position: sticky;
  bottom: -28px;
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  margin: 22px -28px -28px;
  padding: 16px 28px 20px;
  border-top: 1px solid #eef2f7;
  border-radius: 0 0 26px 26px;
  background: rgba(255, 255, 255, 0.94);
  backdrop-filter: blur(12px);
}

/* ---- Upload ảnh minh chứng ---- */
.proof-upload-section {
  margin: 20px 0 4px;
  padding: 18px;
  border: 1px solid #e0e7ff;
  border-radius: 18px;
  background: linear-gradient(180deg, #f8faff, #fff);
}
.proof-upload-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  margin-bottom: 13px;
}
.proof-upload-heading > div { display: flex; flex-direction: column; gap: 4px; }
.proof-upload-heading strong { color: var(--ink); font-size: 13px; font-weight: 900; }
.proof-upload-heading small { color: var(--muted); font-size: 11px; }
.proof-count {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 46px;
  height: 28px;
  padding: 0 10px;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-primary);
  font-size: 11px;
  font-weight: 800;
}
.proof-file-input { display: none !important; }

.proof-picker {
  display: flex;
  align-items: center;
  gap: 13px;
  width: 100%;
  min-height: 82px;
  padding: 14px 16px;
  border: 2px dashed #a5b4fc;
  border-radius: 16px;
  color: var(--primary);
  background: #fff;
  text-align: left;
  cursor: pointer;
  transition: 0.25s var(--ease);
}
.proof-picker:hover:not(:disabled) {
  border-color: var(--primary);
  background: #eef2ff;
  transform: translateY(-2px);
  box-shadow: 0 10px 22px rgba(79, 70, 229, 0.14);
}
.proof-picker:disabled { opacity: 0.55; cursor: not-allowed; }
.proof-picker-icon {
  display: grid;
  place-items: center;
  flex: 0 0 46px;
  width: 46px;
  height: 46px;
  border-radius: 14px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.3);
  font-size: 18px;
}
.proof-picker-copy { display: flex; flex: 1; flex-direction: column; gap: 5px; min-width: 0; }
.proof-picker-copy strong { color: #3730a3; font-size: 12.5px; font-weight: 800; }
.proof-picker-copy small { color: var(--muted); font-size: 11px; }
.proof-picker-plus { color: #818cf8; font-size: 15px; }

.proof-preview-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(96px, 1fr));
  gap: 11px;
  margin-top: 14px;
}
.proof-preview-card {
  position: relative;
  min-width: 0;
  overflow: hidden;
  border: 1px solid var(--line);
  border-radius: 14px;
  background: #fff;
  box-shadow: var(--shadow-sm);
  transition: transform 0.25s var(--ease);
}
.proof-preview-card:hover { transform: translateY(-3px); }
.proof-preview-card img {
  display: block;
  width: 100%;
  height: 88px;
  object-fit: cover;
  background: #eef2f8;
}
.proof-preview-card > span {
  display: block;
  overflow: hidden;
  padding: 6px 8px;
  color: var(--muted);
  font-size: 10px;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.proof-remove-button {
  position: absolute;
  top: 6px;
  right: 6px;
  display: grid;
  place-items: center;
  width: 26px;
  height: 26px;
  border: 1px solid rgba(255, 255, 255, 0.7);
  border-radius: 9px;
  color: #fff;
  background: rgba(15, 23, 42, 0.7);
  cursor: pointer;
  backdrop-filter: blur(6px);
  transition: 0.2s;
}
.proof-remove-button:hover { background: #e11d48; transform: rotate(90deg); }
.proof-upload-hint {
  display: flex;
  align-items: flex-start;
  gap: 8px;
  margin: 11px 0 0;
  color: var(--muted);
  font-size: 10.5px;
  line-height: 1.6;
}
.proof-upload-hint i { margin-top: 1px; color: var(--primary); }

/* ---- Chọn sản phẩm đổi ---- */
.product-picker { position: relative; margin-top: 8px; }
.product-picker-trigger {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  width: 100%;
  min-height: 60px;
  padding: 11px 15px;
  border: 1.5px solid var(--line);
  border-radius: 14px;
  color: #1e293b;
  background: #f8fafc;
  text-align: left;
  cursor: pointer;
  transition: 0.25s var(--ease);
}
.product-picker-trigger:hover,
.product-picker-trigger.is-open {
  border-color: var(--primary);
  background: #fff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}
.product-picker-selected,
.product-picker-option-info {
  display: flex;
  flex-direction: column;
  gap: 5px;
  min-width: 0;
}
.product-picker-selected strong,
.product-picker-option-info strong {
  overflow: hidden;
  color: var(--ink);
  font-size: 12.5px;
  font-weight: 800;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.product-picker-selected small,
.product-picker-option-info small { color: var(--muted); font-size: 11px; }
.product-picker-placeholder { display: flex; align-items: center; gap: 10px; color: #94a3b8; font-size: 13px; }
.product-picker-placeholder i { color: var(--primary); }
.product-picker-arrow { color: #64748b; transition: transform 0.25s var(--ease); }
.product-picker-arrow.rotated { transform: rotate(180deg); }

.product-picker-menu {
  position: absolute;
  z-index: 80;
  top: calc(100% + 8px);
  right: 0;
  left: 0;
  max-height: 320px;
  overflow-y: auto;
  padding: 10px;
  border: 1px solid #e0e7ff;
  border-radius: 18px;
  background: #fff;
  box-shadow: 0 20px 50px rgba(49, 46, 129, 0.22);
  animation: modalIn 0.2s var(--ease) both;
}
.product-picker-search {
  position: sticky;
  z-index: 2;
  top: 0;
  display: flex;
  align-items: center;
  gap: 9px;
  margin-bottom: 8px;
  padding: 0 12px;
  border: 1.5px solid var(--line);
  border-radius: 12px;
  color: #94a3b8;
  background: #f8fafc;
}
.product-picker-search input {
  width: 100%;
  min-height: 40px;
  border: 0;
  outline: 0;
  color: #1e293b;
  background: transparent;
  font: inherit;
  font-size: 12px;
}
.product-picker-option {
  display: flex;
  align-items: center;
  gap: 11px;
  width: 100%;
  padding: 10px;
  border: 0;
  border-radius: 12px;
  background: #fff;
  text-align: left;
  cursor: pointer;
  transition: 0.15s ease;
}
.product-picker-option:hover { background: #eef2ff; }
.product-picker-option.selected { background: #e0e7ff; }
.product-picker-option-icon {
  display: grid;
  place-items: center;
  flex: 0 0 38px;
  width: 38px;
  height: 38px;
  border-radius: 11px;
  color: #fff;
  background: var(--grad-primary);
}
.product-picker-option-info { flex: 1; }
.product-picker-stock {
  flex-shrink: 0;
  padding: 5px 9px;
  border-radius: 999px;
  color: #047857;
  background: #d1fae5;
  font-size: 10px;
  font-weight: 800;
}
.product-picker-empty { padding: 22px 10px; color: var(--muted); font-size: 12px; text-align: center; }

/* =========================================================
   MODAL PHẢN HỒI BÁO GIÁ / THANH TOÁN
   ========================================================= */
.customer-response-modal {
  width: min(540px, calc(100vw - 28px));
  max-height: min(90vh, 780px);
  padding: 26px;
  overflow-y: auto;
  border-radius: 24px;
}
.customer-response-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 15px;
  margin-bottom: 18px;
}
.customer-response-header h2 {
  margin: 8px 0 5px;
  font-size: 22px;
  font-weight: 900;
  letter-spacing: -0.4px;
  background: linear-gradient(90deg, #0f172a 0%, #4f46e5 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.customer-response-header p { margin: 0; color: var(--muted); font-size: 12px; }
.customer-response-intro { margin-bottom: 15px; color: #475569; font-size: 13px; line-height: 1.75; }
.customer-response-money {
  display: grid;
  gap: 11px;
  padding: 16px;
  border: 1px solid #c7d2fe;
  border-radius: 16px;
  background: linear-gradient(180deg, #eef2ff, #f8faff);
}
.customer-response-money > div {
  display: flex;
  justify-content: space-between;
  gap: 12px;
  color: #64748b;
  font-size: 12.5px;
}
.customer-response-money strong { color: #1e293b; text-align: right; }
.customer-response-money .customer-response-total {
  padding-top: 12px;
  border-top: 1px dashed #a5b4fc;
  color: #3730a3;
  font-weight: 800;
}
.customer-response-money .customer-response-total strong {
  font-size: 22px;
  font-weight: 900;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.customer-response-note,
.customer-response-warning {
  margin: 14px 0 0;
  padding: 11px 14px;
  border-radius: 12px;
  color: #475569;
  background: #f1f5f9;
  font-size: 11.5px;
  line-height: 1.7;
}
.customer-response-warning { color: #92400e; background: #fffbeb; border: 1px solid #fde68a; }
.customer-response-field { display: block; margin-top: 15px; color: #334155; font-size: 12.5px; font-weight: 800; }
.customer-response-field b { color: #e11d48; }
.customer-response-field input,
.customer-response-field select {
  display: block;
  width: 100%;
  min-height: 46px;
  margin-top: 8px;
  padding: 10px 14px;
  border: 1.5px solid var(--line);
  border-radius: 12px;
  outline: none;
  color: #1e293b;
  background: #f8fafc;
  font-size: 13px;
  transition: all 0.25s var(--ease);
}
.customer-response-field input:focus,
.customer-response-field select:focus {
  border-color: var(--primary);
  background: #fff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}
.customer-response-actions {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 22px;
}

/* =========================================================
   FOOTER TRANG
   ========================================================= */
.history-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 18px;
  margin-top: 28px;
  padding: 20px 4px 0;
  border-top: 1px solid var(--line);
  color: #94a3b8;
  font-size: 11.5px;
}
.history-footer strong {
  font-size: 13px;
  font-weight: 900;
  letter-spacing: 2px;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

/* =========================================================
   RESPONSIVE
   ========================================================= */
@media (max-width: 1000px) {
  .stats-grid { grid-template-columns: repeat(2, minmax(0, 1fr)); }
}

@media (max-width: 650px) {
  .history-page { padding: 18px 12px; }

  .history-header {
    align-items: flex-start;
    flex-direction: column;
    padding: 24px 20px;
    border-radius: 20px;
  }
  .history-header h1 { font-size: 30px; }
  .refresh-button { align-self: flex-start; }

  .stats-grid { gap: 10px; }
  .stat-card { gap: 11px; padding: 14px 12px; border-radius: 16px; }
  .stat-icon { flex-basis: 40px; width: 40px; height: 40px; border-radius: 12px; font-size: 16px; }
  .stat-card strong { font-size: 24px; }
  .stat-card small { display: none; }

  .orders-toolbar { padding: 16px; border-radius: 18px; }
  .toolbar-heading h2 { font-size: 20px; }

  .order-card { border-radius: 18px; }
  .order-card-header { align-items: flex-start; flex-direction: column; padding: 20px 16px 14px; }
  .order-progress { padding: 18px 8px; }
  .progress-step small { max-width: 76px; font-size: 9.5px; line-height: 1.4; }
  .progress-step:not(:last-child)::after { left: calc(50% + 16px); width: calc(100% - 32px); }
  .order-products { padding: 4px 14px; }
  .order-product { gap: 10px; }
  .order-product-image { width: 60px; height: 60px; border-radius: 14px; }
  .order-product-info > strong { font-size: 12.5px; }
  .order-product-price { font-size: 12.5px; }
  .order-card-footer { align-items: flex-start; flex-direction: column; padding: 16px; }
  .order-total { width: 100%; justify-content: space-between; }
  .order-actions { flex-wrap: wrap; padding: 0 16px 16px; }
  .order-actions button { flex: 1; }

  .modal-backdrop-custom { align-items: center; padding: 10px; }
  .order-modal { max-height: calc(100dvh - 20px); border-radius: 20px; }
  .modal-header { padding: 20px 18px; }
  .modal-header h2 { font-size: 21px; }
  .modal-content { padding: 18px; }
  .modal-footer { padding: 14px 18px; }
  .detail-info-grid { grid-template-columns: 1fr; }
  .detail-info-grid .detail-full { grid-column: auto; }
  .detail-product-image { width: 52px; height: 52px; }
  .detail-total-line strong { font-size: 20px; }

  .warranty-snapshot-grid { grid-template-columns: 1fr; }
  .customer-request-actions { align-items: stretch; }

  .cancel-modal { padding: 24px 18px; border-radius: 20px; }
  .cancel-modal-actions { flex-direction: column-reverse; }

  .customer-request-modal { padding: 18px; border-radius: 20px; max-height: calc(100dvh - 20px); }
  .customer-request-modal-actions { bottom: -18px; margin: 20px -18px -18px; padding: 14px 18px 16px; border-radius: 0 0 20px 20px; }
  .customer-request-modal-actions button { flex: 1; }
  .proof-upload-section { padding: 13px; }
  .proof-preview-grid { grid-template-columns: repeat(3, minmax(0, 1fr)); }
  .product-picker-menu { max-height: 260px; }
  .product-picker-stock { font-size: 9px; }

  .request-tracking-heading { flex-direction: column; }
  .request-status-pill { align-self: flex-start; }
  .request-quote-summary,
  .request-refund-summary { grid-template-columns: 1fr; }
  .request-quote-summary .request-customer-amount { grid-column: auto; }

  .customer-response-modal { padding: 18px; border-radius: 20px; }
  .customer-response-actions { flex-direction: column-reverse; }
  .customer-response-actions button { width: 100%; }

  .history-footer { align-items: flex-start; flex-direction: column; gap: 7px; }
}

/* Tôn trọng người dùng tắt hiệu ứng chuyển động */
@media (prefers-reduced-motion: reduce) {
  .history-page *,
  .history-page *::before,
  .history-page *::after,
  .modal-backdrop-custom,
  .modal-backdrop-custom *,
  .modal-backdrop-custom *::before {
    animation: none !important;
    transition: none !important;
  }
}
</style>