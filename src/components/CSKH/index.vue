
<template>
  <div class="cs-page">
    <div class="cs-heading">
      <div>
        <span class="cs-eyebrow">TECHSHOP • CUSTOMER SERVICE</span>
        <h1>Chăm sóc khách hàng</h1>
        <p>Tiếp nhận và xử lý các yêu cầu bảo hành, đổi trả của khách hàng.</p>
      </div>

      <button class="btn-refresh" type="button" @click="taiLai" :disabled="dangTai">
        <i class="fa-solid fa-rotate" :class="{ spinning: dangTai }"></i>
        Làm mới
      </button>
    </div>

    <!-- THỐNG KÊ -->
    <div class="cs-stats">
      <div class="stat-card">
        <div class="stat-icon blue"><i class="fa-solid fa-shield-halved"></i></div>
        <div>
          <span>Yêu cầu bảo hành</span>
          <strong>{{ danhSachBaoHanh.length }}</strong>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon orange"><i class="fa-solid fa-arrows-rotate"></i></div>
        <div>
          <span>Yêu cầu đổi trả</span>
          <strong>{{ danhSachDoiTra.length }}</strong>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon purple"><i class="fa-solid fa-clock"></i></div>
        <div>
          <span>Bảo hành đang chờ</span>
          <strong>{{ baoHanhDangCho }}</strong>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon green"><i class="fa-solid fa-box"></i></div>
        <div>
          <span>Đổi trả đang chờ</span>
          <strong>{{ doiTraDangCho }}</strong>
        </div>
      </div>
    </div>

    <!-- TAB -->
    <div class="cs-tabs">
      <button
        type="button"
        :class="{ active: tabHienTai === 'bao-hanh' }"
        @click="doiTab('bao-hanh')"
      >
        <i class="fa-solid fa-shield-halved"></i>
        Bảo hành
        <span>{{ danhSachBaoHanh.length }}</span>
      </button>

      <button
        type="button"
        :class="{ active: tabHienTai === 'doi-tra' }"
        @click="doiTab('doi-tra')"
      >
        <i class="fa-solid fa-arrows-rotate"></i>
        Đổi trả
        <span>{{ danhSachDoiTra.length }}</span>
      </button>
    </div>

    <!-- THANH TÌM KIẾM VÀ LỌC -->
    <div class="cs-toolbar">
      <div class="cs-search">
        <i class="fa-solid fa-magnifying-glass"></i>
        <input
          v-model="keyword"
          type="text"
          placeholder="Tìm tên khách hàng, sản phẩm, email, số điện thoại..."
          @keyup.enter="locDuLieu"
        />
        <button
          v-if="keyword"
          type="button"
          @click="keyword = ''; locDuLieu()"
        >
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <select v-model="locTrangThai" @change="locDuLieu">
        <option value="">Tất cả trạng thái</option>
        <option
          v-for="item in danhSachTrangThaiLoc"
          :key="item.value"
          :value="item.value"
        >
          {{ item.label }}
        </option>
      </select>

      <select
        v-if="tabHienTai === 'doi-tra'"
        v-model="locLoaiYeuCau"
        @change="locDuLieu"
      >
        <option value="">Tất cả loại yêu cầu</option>
        <option value="DoiHang">Đổi hàng</option>
        <option value="TraHangHoanTien">Trả hàng hoàn tiền</option>
      </select>

      <button class="btn-filter" type="button" @click="locDuLieu">
        <i class="fa-solid fa-filter"></i>
        Lọc
      </button>
    </div>

    <div v-if="loi" class="cs-error">
      <i class="fa-solid fa-circle-exclamation"></i>
      {{ loi }}
    </div>

    <!-- DANH SÁCH BẢO HÀNH -->
    <div v-if="tabHienTai === 'bao-hanh'" class="cs-panel">
      <div class="panel-heading">
        <div>
          <h3>Danh sách yêu cầu bảo hành</h3>
          <p>Quản lý tiếp nhận, kiểm tra, báo giá và tiến độ bảo hành.</p>
        </div>
        <span class="total-label">{{ danhSachBaoHanhLoc.length }} yêu cầu</span>
      </div>

      <div v-if="dangTai" class="loading-state">
        <i class="fa-solid fa-spinner spinning"></i>
        Đang tải dữ liệu...
      </div>

      <div v-else-if="!danhSachBaoHanhLoc.length" class="empty-state">
        <i class="fa-solid fa-shield-halved"></i>
        <strong>Chưa có yêu cầu bảo hành</strong>
        <span>Yêu cầu của khách hàng sẽ xuất hiện tại đây.</span>
      </div>

      <div v-else class="table-responsive">
        <table class="cs-table">
          <thead>
            <tr>
              <th>Mã yêu cầu</th>
              <th>Khách hàng</th>
              <th>Sản phẩm</th>
              <th>Ngày yêu cầu</th>
              <th>Chi phí khách trả</th>
              <th>Trạng thái</th>
              <th>Thao tác</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="item in danhSachBaoHanhLoc" :key="item.MaYCBH">
              <td>
                <strong class="request-code">#BH{{ item.MaYCBH }}</strong>
              </td>

              <td>
                <div class="customer-cell">
                  <strong>{{ getKhachHang(item).HoTen || 'Chưa có tên' }}</strong>
                  <small>{{ getKhachHang(item).SoDienThoai || getKhachHang(item).Email || '—' }}</small>
                </div>
              </td>

              <td>
                <div class="product-cell">
                  <img
                    v-if="getSanPham(item).HinhAnh"
                    :src="getSanPham(item).HinhAnh"
                    :alt="getSanPham(item).TenSP"
                  />
                  <div class="product-cell-info">
                    <strong>{{ getSanPham(item).TenSP || 'Không có dữ liệu' }}</strong>
                    <small>{{ getDonHang(item).MaDH ? 'Đơn #' + getDonHang(item).MaDH : '' }}</small>
                  </div>
                </div>
              </td>

              <td>{{ dinhDangNgay(item.NgayYeuCau) }}</td>

              <td class="money-cell">
                {{ item.SoTienKhachTra != null ? dinhDangTien(item.SoTienKhachTra) : 'Chưa báo giá' }}
              </td>

              <td>
                <span class="status-pill" :class="classTrangThai(item.TrangThai)">
                  {{ tenTrangThai(item.TrangThai) }}
                </span>
              </td>

              <td>
                <button class="btn-detail" type="button" @click="moChiTietBaoHanh(item)">
                  <i class="fa-solid fa-eye"></i>
                  Chi tiết
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- DANH SÁCH ĐỔI TRẢ -->
    <div v-else class="cs-panel">
      <div class="panel-heading">
        <div>
          <h3>Danh sách yêu cầu đổi trả</h3>
          <p>Quản lý yêu cầu đổi hàng và trả hàng hoàn tiền.</p>
        </div>
        <span class="total-label">{{ danhSachDoiTraLoc.length }} yêu cầu</span>
      </div>

      <div v-if="dangTai" class="loading-state">
        <i class="fa-solid fa-spinner spinning"></i>
        Đang tải dữ liệu...
      </div>

      <div v-else-if="!danhSachDoiTraLoc.length" class="empty-state">
        <i class="fa-solid fa-box-open"></i>
        <strong>Chưa có yêu cầu đổi trả</strong>
        <span>Yêu cầu đổi hàng và trả hàng sẽ xuất hiện tại đây.</span>
      </div>

      <div v-else class="table-responsive">
        <table class="cs-table">
          <thead>
            <tr>
              <th>Mã yêu cầu</th>
              <th>Khách hàng</th>
              <th>Sản phẩm</th>
              <th>Loại yêu cầu</th>
              <th>Số lượng</th>
              <th>Ngày yêu cầu</th>
              <th>Trạng thái</th>
              <th>Thao tác</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="item in danhSachDoiTraLoc" :key="item.MaYCDT">
              <td>
                <strong class="request-code">#DT{{ item.MaYCDT }}</strong>
              </td>

              <td>
                <div class="customer-cell">
                  <strong>{{ getKhachHang(item).HoTen || 'Chưa có tên' }}</strong>
                  <small>{{ getKhachHang(item).SoDienThoai || getKhachHang(item).Email || '—' }}</small>
                </div>
              </td>

              <td>
                <div class="product-cell">
                  <img
                    v-if="getSanPham(item).HinhAnh"
                    :src="getSanPham(item).HinhAnh"
                    :alt="getSanPham(item).TenSP"
                  />
                  <div class="product-cell-info">
                    <strong>{{ getSanPham(item).TenSP || 'Không có dữ liệu' }}</strong>
                    <small>{{ getDonHang(item).MaDH ? 'Đơn #' + getDonHang(item).MaDH : '' }}</small>
                  </div>
                </div>
              </td>

              <td>
                <span
                  class="type-pill"
                  :class="item.LoaiYeuCau === 'DoiHang' ? 'type-exchange' : 'type-refund'"
                >
                  {{ item.LoaiYeuCau === 'DoiHang' ? 'Đổi hàng' : 'Trả hàng hoàn tiền' }}
                </span>
              </td>

              <td>{{ item.SoLuongYeuCau ?? 0 }}</td>
              <td>{{ dinhDangNgay(item.NgayYeuCau) }}</td>

              <td>
                <span class="status-pill" :class="classTrangThai(item.TrangThai)">
                  {{ tenTrangThai(item.TrangThai) }}
                </span>
              </td>

              <td>
                <button class="btn-detail" type="button" @click="moChiTietDoiTra(item)">
                  <i class="fa-solid fa-eye"></i>
                  Chi tiết
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- MODAL CHI TIẾT BẢO HÀNH -->
    <div v-if="hienModalBaoHanh" class="cs-modal-backdrop" @click.self="dongModal">
      <div class="cs-modal">
        <div class="modal-heading">
          <div>
            <span class="cs-eyebrow">WARRANTY REQUEST</span>
            <h2>Chi tiết yêu cầu #BH{{ baoHanhDangChon?.MaYCBH }}</h2>
          </div>
          <button class="modal-close" type="button" @click="dongModal">
            <i class="fa-solid fa-xmark"></i>
          </button>
        </div>

        <div v-if="dangTaiChiTiet" class="loading-state">
          <i class="fa-solid fa-spinner spinning"></i>
          Đang tải chi tiết...
        </div>

        <template v-else-if="baoHanhDangChon">
          <div class="detail-status-row">
            <span class="status-pill" :class="classTrangThai(baoHanhDangChon.TrangThai)">
              {{ tenTrangThai(baoHanhDangChon.TrangThai) }}
            </span>
            <span class="detail-date">Ngày yêu cầu: {{ dinhDangNgay(baoHanhDangChon.NgayYeuCau) }}</span>
          </div>

          <div class="detail-grid">
            <section class="detail-box">
              <h4><i class="fa-solid fa-user"></i> Khách hàng</h4>
              <p><b>Họ tên:</b> {{ getKhachHang(baoHanhDangChon).HoTen || '—' }}</p>
              <p><b>Email:</b> {{ getKhachHang(baoHanhDangChon).Email || '—' }}</p>
              <p><b>Điện thoại:</b> {{ getKhachHang(baoHanhDangChon).SoDienThoai || '—' }}</p>
              <p><b>Mã đơn:</b> #{{ getDonHang(baoHanhDangChon).MaDH || '—' }}</p>
              <p><b>Ngày giao:</b> {{ dinhDangNgay(getDonHang(baoHanhDangChon).NgayGiao) }}</p>
            </section>

            <section class="detail-box">
              <h4><i class="fa-solid fa-box"></i> Sản phẩm</h4>
              <div class="detail-product">
                <img
                  v-if="getSanPham(baoHanhDangChon).HinhAnh"
                  :src="getSanPham(baoHanhDangChon).HinhAnh"
                  :alt="getSanPham(baoHanhDangChon).TenSP"
                />
                <div>
                  <strong>{{ getSanPham(baoHanhDangChon).TenSP || '—' }}</strong>
                  <small>{{ getSanPham(baoHanhDangChon).ThuongHieu || 'Chưa có thương hiệu' }}</small>
                </div>
              </div>
              <p><b>Mã chi tiết đơn hàng:</b> {{ baoHanhDangChon.MaCTDH || getChiTietDonHang(baoHanhDangChon).MaCTDH || '—' }}</p>
              <p><b>Ngày tiếp nhận:</b> {{ dinhDangNgay(baoHanhDangChon.NgayTiepNhan) }}</p>
            </section>
          </div>

          <section class="detail-box full-width">
            <h4><i class="fa-solid fa-triangle-exclamation"></i> Tình trạng lỗi khách mô tả</h4>
            <p class="description-text">{{ baoHanhDangChon.TinhTrangLoi || 'Không có mô tả.' }}</p>

            <div v-if="layDanhSachAnh(baoHanhDangChon.HinhAnhMinhChung).length" class="evidence-list">
              <a
                v-for="(anh, index) in layDanhSachAnh(baoHanhDangChon.HinhAnhMinhChung)"
                :key="index"
                :href="anh"
                target="_blank"
                rel="noopener noreferrer"
              >
                <img :src="anh" :alt="'Ảnh minh chứng ' + (index + 1)" />
              </a>
            </div>
          </section>

          <section class="detail-box full-width">
            <h4><i class="fa-solid fa-calculator"></i> Thông tin báo giá</h4>
            <div class="quote-grid">
              <div><span>Chi phí dự kiến</span><strong>{{ dinhDangTien(baoHanhDangChon.ChiPhiDuKien) }}</strong></div>
              <div><span>Tỷ lệ shop hỗ trợ</span><strong>{{ baoHanhDangChon.TyLeShopHoTroApDung ?? '—' }}%</strong></div>
              <div><span>Shop hỗ trợ</span><strong class="money-green">{{ dinhDangTien(baoHanhDangChon.SoTienShopHoTro) }}</strong></div>
              <div><span>Khách thanh toán</span><strong class="money-orange">{{ dinhDangTien(baoHanhDangChon.SoTienKhachTra) }}</strong></div>
            </div>
            <p v-if="baoHanhDangChon.GhiChuBaoGia">
              <b>Ghi chú báo giá:</b> {{ baoHanhDangChon.GhiChuBaoGia }}
            </p>
            <p v-if="baoHanhDangChon.PhuongThucThanhToan">
              <b>Phương thức thanh toán:</b> {{ baoHanhDangChon.PhuongThucThanhToan }}
            </p>
            <p v-if="baoHanhDangChon.MaGiaoDichThanhToan">
              <b>Mã giao dịch:</b> {{ baoHanhDangChon.MaGiaoDichThanhToan }}
            </p>
          </section>

          <section v-if="baoHanhDangChon.KetQuaXuLy || baoHanhDangChon.LyDoTuChoi" class="detail-box full-width">
            <h4><i class="fa-solid fa-clipboard-check"></i> Kết quả xử lý</h4>
            <p v-if="baoHanhDangChon.KetQuaXuLy">{{ baoHanhDangChon.KetQuaXuLy }}</p>
            <p v-if="baoHanhDangChon.LyDoTuChoi" class="text-danger">{{ baoHanhDangChon.LyDoTuChoi }}</p>
          </section>

          <!-- LẬP BÁO GIÁ -->
          <section v-if="baoHanhDangChon.TrangThai === 'DangKiemTra'" class="action-box">
            <h4><i class="fa-solid fa-file-invoice-dollar"></i> Lập báo giá bảo hành</h4>
            <p>Nhập tổng chi phí sửa chữa. Hệ thống tự tính tiền shop hỗ trợ và tiền khách cần thanh toán theo tỷ lệ đã lưu.</p>

            <div class="form-grid">
              <label>
                Chi phí sửa chữa dự kiến (VNĐ) *
                <input
                  v-model.number="formBaoGia.ChiPhiDuKien"
                  type="number"
                  min="0"
                  step="1000"
                  placeholder="Nhập tổng chi phí"
                />
              </label>

              <label>
                Ghi chú báo giá
                <textarea
                  v-model="formBaoGia.GhiChuBaoGia"
                  rows="3"
                  maxlength="5000"
                  placeholder="Mô tả hạng mục sửa chữa..."
                ></textarea>
              </label>
            </div>

            <button class="btn-primary" type="button" @click="lapBaoGia" :disabled="dangXuLy">
              <i class="fa-solid fa-paper-plane"></i>
              {{ dangXuLy ? 'Đang gửi...' : 'Gửi báo giá cho khách' }}
            </button>
          </section>

          <!-- XÁC NHẬN THANH TOÁN -->
          <section v-if="baoHanhDangChon.TrangThai === 'ChoXacNhanThanhToan'" class="action-box payment-box">
            <h4><i class="fa-solid fa-money-bill-transfer"></i> Khách đã thông báo thanh toán</h4>
            <p>Kiểm tra giao dịch thực tế trước khi xác nhận đã nhận tiền.</p>
            <button class="btn-primary" type="button" @click="xacNhanThanhToan" :disabled="dangXuLy">
              <i class="fa-solid fa-check"></i>
              {{ dangXuLy ? 'Đang xác nhận...' : 'Xác nhận đã nhận thanh toán' }}
            </button>
          </section>

          <!-- CẬP NHẬT TIẾN ĐỘ BẢO HÀNH -->
          <section v-if="trangThaiBaoHanhTiepTheo.length" class="action-box">
            <h4><i class="fa-solid fa-list-check"></i> Cập nhật tiến độ bảo hành</h4>

            <label>
              Trạng thái tiếp theo
              <select v-model="formBaoHanh.TrangThai">
                <option value="">-- Chọn trạng thái --</option>
                <option
                  v-for="status in trangThaiBaoHanhTiepTheo"
                  :key="status"
                  :value="status"
                >
                  {{ tenTrangThai(status) }}
                </option>
              </select>
            </label>

            <label v-if="formBaoHanh.TrangThai === 'TuChoi'">
              Lý do từ chối *
              <textarea
                v-model="formBaoHanh.LyDoTuChoi"
                rows="3"
                maxlength="255"
                placeholder="Nhập lý do từ chối..."
              ></textarea>
            </label>

            <label v-if="formBaoHanh.TrangThai === 'HoanTatBaoHanh'">
              Kết quả sửa chữa/bảo hành *
              <textarea
                v-model="formBaoHanh.KetQuaXuLy"
                rows="3"
                maxlength="5000"
                placeholder="Nhập kết quả xử lý..."
              ></textarea>
            </label>

            <p v-if="formBaoHanh.TrangThai === 'TuChoi' && baoHanhDangChon.NgayTiepNhan" class="warning-note">
              <i class="fa-solid fa-circle-info"></i>
              Shop đã nhận sản phẩm. BE sẽ chuyển yêu cầu sang trạng thái gửi trả khách thay vì kết thúc ngay.
            </p>

            <button
              class="btn-primary"
              type="button"
              @click="capNhatTrangThaiBaoHanh"
              :disabled="dangXuLy || !formBaoHanh.TrangThai"
            >
              <i class="fa-solid fa-floppy-disk"></i>
              {{ dangXuLy ? 'Đang cập nhật...' : 'Cập nhật trạng thái' }}
            </button>
          </section>
        </template>

        <div class="modal-footer">
          <button class="btn-secondary" type="button" @click="dongModal">Đóng</button>
        </div>
      </div>
    </div>

    <!-- MODAL CHI TIẾT ĐỔI TRẢ -->
    <div v-if="hienModalDoiTra" class="cs-modal-backdrop" @click.self="dongModal">
      <div class="cs-modal">
        <div class="modal-heading">
          <div>
            <span class="cs-eyebrow">RETURN & EXCHANGE REQUEST</span>
            <h2>Chi tiết yêu cầu #DT{{ doiTraDangChon?.MaYCDT }}</h2>
          </div>
          <button class="modal-close" type="button" @click="dongModal">
            <i class="fa-solid fa-xmark"></i>
          </button>
        </div>

        <div v-if="dangTaiChiTiet" class="loading-state">
          <i class="fa-solid fa-spinner spinning"></i>
          Đang tải chi tiết...
        </div>

        <template v-else-if="doiTraDangChon">
          <div class="detail-status-row">
            <span class="status-pill" :class="classTrangThai(doiTraDangChon.TrangThai)">
              {{ tenTrangThai(doiTraDangChon.TrangThai) }}
            </span>
            <span class="type-pill" :class="doiTraDangChon.LoaiYeuCau === 'DoiHang' ? 'type-exchange' : 'type-refund'">
              {{ doiTraDangChon.LoaiYeuCau === 'DoiHang' ? 'Đổi hàng' : 'Trả hàng hoàn tiền' }}
            </span>
            <span class="detail-date">Ngày yêu cầu: {{ dinhDangNgay(doiTraDangChon.NgayYeuCau) }}</span>
          </div>

          <div class="detail-grid">
            <section class="detail-box">
              <h4><i class="fa-solid fa-user"></i> Khách hàng</h4>
              <p><b>Họ tên:</b> {{ getKhachHang(doiTraDangChon).HoTen || '—' }}</p>
              <p><b>Email:</b> {{ getKhachHang(doiTraDangChon).Email || '—' }}</p>
              <p><b>Điện thoại:</b> {{ getKhachHang(doiTraDangChon).SoDienThoai || '—' }}</p>
              <p><b>Mã đơn:</b> #{{ getDonHang(doiTraDangChon).MaDH || '—' }}</p>
              <p><b>Ngày giao:</b> {{ dinhDangNgay(getDonHang(doiTraDangChon).NgayGiao) }}</p>
            </section>

            <section class="detail-box">
              <h4><i class="fa-solid fa-box"></i> Sản phẩm</h4>
              <div class="detail-product">
                <img
                  v-if="getSanPham(doiTraDangChon).HinhAnh"
                  :src="getSanPham(doiTraDangChon).HinhAnh"
                  :alt="getSanPham(doiTraDangChon).TenSP"
                />
                <div>
                  <strong>{{ getSanPham(doiTraDangChon).TenSP || '—' }}</strong>
                  <small>{{ getSanPham(doiTraDangChon).ThuongHieu || 'Chưa có thương hiệu' }}</small>
                </div>
              </div>
              <p><b>Số lượng yêu cầu:</b> {{ doiTraDangChon.SoLuongYeuCau }}</p>
              <p><b>Mã chi tiết đơn hàng:</b> {{ doiTraDangChon.MaCTDH || getChiTietDonHang(doiTraDangChon).MaCTDH || '—' }}</p>
            </section>
          </div>

          <section class="detail-box full-width">
            <h4><i class="fa-solid fa-comment-dots"></i> Nội dung yêu cầu</h4>
            <p><b>Lý do:</b> {{ doiTraDangChon.LyDoYeuCau || '—' }}</p>
            <p><b>Mô tả chi tiết:</b></p>
            <p class="description-text">{{ doiTraDangChon.MoTaChiTiet || 'Không có mô tả.' }}</p>

            <div v-if="layDanhSachAnh(doiTraDangChon.HinhAnhMinhChung).length" class="evidence-list">
              <a
                v-for="(anh, index) in layDanhSachAnh(doiTraDangChon.HinhAnhMinhChung)"
                :key="index"
                :href="anh"
                target="_blank"
                rel="noopener noreferrer"
              >
                <img :src="anh" :alt="'Ảnh minh chứng ' + (index + 1)" />
              </a>
            </div>
          </section>

          <section class="detail-box full-width">
            <h4><i class="fa-solid fa-truck"></i> Thông tin xử lý</h4>
            <p><b>Sản phẩm đổi:</b> {{ getSanPhamDoi(doiTraDangChon).TenSP || 'Chưa có sản phẩm đổi' }}</p>
            <p><b>Mã sản phẩm đổi:</b> {{ doiTraDangChon.MaSPDoi || '—' }}</p>
            <p><b>Ngày shop nhận hàng:</b> {{ dinhDangNgay(doiTraDangChon.NgayNhanHang) }}</p>
            <p><b>Lý do từ chối:</b> {{ doiTraDangChon.LyDoTuChoi || '—' }}</p>
            <p><b>Số tiền hoàn:</b> {{ doiTraDangChon.SoTienHoan != null ? dinhDangTien(doiTraDangChon.SoTienHoan) : 'Chưa hoàn tiền' }}</p>
            <p><b>Phương thức hoàn tiền:</b> {{ doiTraDangChon.PhuongThucHoanTien || '—' }}</p>
            <p><b>Ngày hoàn tiền:</b> {{ dinhDangNgay(doiTraDangChon.NgayHoanTien) }}</p>
          </section>

          <!-- XÁC NHẬN HOÀN TIỀN -->
          <section
            v-if="doiTraDangChon.LoaiYeuCau === 'TraHangHoanTien' && doiTraDangChon.TrangThai === 'DangXuLy'"
            class="action-box"
          >
            <h4><i class="fa-solid fa-money-bill-transfer"></i> Xác nhận hoàn tiền</h4>
            <p>Chỉ sử dụng khi đã kiểm tra hàng và sẵn sàng hoàn tiền cho khách.</p>

            <div class="form-grid">
              <label>
                Số tiền hoàn (VNĐ) *
                <input
                  v-model.number="formHoanTien.SoTienHoan"
                  type="number"
                  min="0"
                  step="1000"
                  placeholder="Nhập số tiền hoàn"
                />
              </label>

              <label>
                Phương thức hoàn tiền *
                <select v-model="formHoanTien.PhuongThucHoanTien">
                  <option value="">-- Chọn phương thức --</option>
                  <option value="ChuyenKhoan">Chuyển khoản</option>
                  <option value="TienMat">Tiền mặt</option>
                </select>
              </label>
            </div>

            <button class="btn-primary" type="button" @click="xacNhanHoanTien" :disabled="dangXuLy">
              <i class="fa-solid fa-check"></i>
              {{ dangXuLy ? 'Đang xử lý...' : 'Xác nhận đã hoàn tiền' }}
            </button>
          </section>

          <!-- CẬP NHẬT TIẾN ĐỘ ĐỔI TRẢ -->
          <section v-if="trangThaiDoiTraTiepTheo.length" class="action-box">
            <h4><i class="fa-solid fa-list-check"></i> Cập nhật tiến độ đổi trả</h4>

            <label>
              Trạng thái tiếp theo
              <select v-model="formDoiTra.TrangThai">
                <option value="">-- Chọn trạng thái --</option>
                <option
                  v-for="status in trangThaiDoiTraTiepTheo"
                  :key="status"
                  :value="status"
                >
                  {{ tenTrangThai(status) }}
                </option>
              </select>
            </label>

            <label v-if="formDoiTra.TrangThai === 'TuChoi'">
              Lý do từ chối *
              <textarea
                v-model="formDoiTra.LyDoTuChoi"
                rows="3"
                maxlength="255"
                placeholder="Nhập lý do từ chối..."
              ></textarea>
            </label>

            <p
              v-if="formDoiTra.TrangThai === 'TuChoi' && doiTraDangChon.NgayNhanHang"
              class="warning-note"
            >
              <i class="fa-solid fa-circle-info"></i>
              Shop đã nhận hàng. BE sẽ chuyển sang gửi trả sản phẩm cho khách.
            </p>

            <p
              v-if="formDoiTra.TrangThai === 'DangVanChuyenHangDoi' && !doiTraDangChon.MaSPDoi"
              class="warning-note"
            >
              <i class="fa-solid fa-triangle-exclamation"></i>
              Chưa có mã sản phẩm đổi. BE sẽ không cho phép giao hàng đổi.
            </p>

            <button
              class="btn-primary"
              type="button"
              @click="capNhatTrangThaiDoiTra"
              :disabled="dangXuLy || !formDoiTra.TrangThai"
            >
              <i class="fa-solid fa-floppy-disk"></i>
              {{ dangXuLy ? 'Đang cập nhật...' : 'Cập nhật trạng thái' }}
            </button>
          </section>
        </template>

        <div class="modal-footer">
          <button class="btn-secondary" type="button" @click="dongModal">Đóng</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

const API = "http://127.0.0.1:8000/api";

const TEN_TRANG_THAI = {
  ChoXacNhan: "Chờ xác nhận",
  DaXacNhan: "Đã xác nhận",
  ChoKhachGuiHang: "Chờ khách gửi hàng",
  DangVanChuyenVeShop: "Đang vận chuyển về shop",
  DaNhanHang: "Shop đã nhận hàng",
  DangKiemTra: "Đang kiểm tra",
  ChoKhachXacNhanBaoGia: "Chờ khách xác nhận báo giá",
  ChoThanhToan: "Chờ khách thanh toán",
  ChoXacNhanThanhToan: "Chờ xác nhận thanh toán",
  DaThanhToan: "Đã thanh toán",
  ChoBaoHanh: "Chờ bảo hành",
  DangBaoHanh: "Đang bảo hành",
  HoanTatBaoHanh: "Đã hoàn tất bảo hành",
  DangVanChuyenTraKhach: "Đang gửi trả khách",
  DaGiaoLaiKhach: "Đã giao lại khách",
  HoanThanh: "Hoàn thành",
  KhachTuChoiBaoGia: "Khách từ chối báo giá",
  DangXuLy: "Đang xử lý",
  DangVanChuyenHangDoi: "Đang giao hàng đổi",
  DaGiaoHangDoi: "Đã giao hàng đổi",
  DaHoanTien: "Đã hoàn tiền",
  TuChoi: "Từ chối",
  DaHuy: "Đã hủy",
};

const LUONG_BAO_HANH = {
  ChoXacNhan: ["DaXacNhan", "TuChoi"],
  DaXacNhan: ["ChoKhachGuiHang", "DaNhanHang", "TuChoi"],
  ChoKhachGuiHang: ["DangVanChuyenVeShop", "DaNhanHang", "TuChoi"],
  DangVanChuyenVeShop: ["DaNhanHang"],
  DaNhanHang: ["DangKiemTra", "TuChoi"],
  DangKiemTra: ["TuChoi"],
  ChoBaoHanh: ["DangBaoHanh"],
  DaThanhToan: ["DangBaoHanh"],
  DangBaoHanh: ["HoanTatBaoHanh"],
  HoanTatBaoHanh: ["DangVanChuyenTraKhach"],
  KhachTuChoiBaoGia: ["DangVanChuyenTraKhach"],
  TuChoi: ["DangVanChuyenTraKhach"],
  DangVanChuyenTraKhach: ["DaGiaoLaiKhach"],
  DaGiaoLaiKhach: ["HoanThanh"],
};

const LUONG_DOI_TRA = {
  ChoXacNhan: ["DaXacNhan", "TuChoi"],
  DaXacNhan: ["ChoKhachGuiHang", "DaNhanHang", "TuChoi"],
  ChoKhachGuiHang: ["DangVanChuyenVeShop", "DaNhanHang", "TuChoi"],
  DangVanChuyenVeShop: ["DaNhanHang"],
  DaNhanHang: ["DangKiemTra"],
  DangKiemTra: ["DangXuLy", "TuChoi"],
  DangXuLy: ["DangVanChuyenHangDoi", "TuChoi"],
  DangVanChuyenHangDoi: ["DaGiaoHangDoi"],
  DaGiaoHangDoi: ["HoanThanh"],
  DaHoanTien: ["HoanThanh"],
  DangVanChuyenTraKhach: ["DaGiaoLaiKhach"],
  DaGiaoLaiKhach: ["HoanThanh"],
};

export default {
  name: "ChamSocKhachHang",

  data() {
    return {
      tabHienTai: "bao-hanh",

      danhSachBaoHanh: [],
      danhSachDoiTra: [],

      keyword: "",
      locTrangThai: "",
      locLoaiYeuCau: "",

      dangTai: false,
      dangTaiChiTiet: false,
      dangXuLy: false,
      loi: "",

      hienModalBaoHanh: false,
      hienModalDoiTra: false,

      baoHanhDangChon: null,
      doiTraDangChon: null,

      formBaoGia: {
        ChiPhiDuKien: "",
        GhiChuBaoGia: "",
      },

      formBaoHanh: {
        TrangThai: "",
        LyDoTuChoi: "",
        KetQuaXuLy: "",
      },

      formDoiTra: {
        TrangThai: "",
        LyDoTuChoi: "",
      },

      formHoanTien: {
        SoTienHoan: "",
        PhuongThucHoanTien: "",
      },
    };
  },

  computed: {
    danhSachTrangThaiLoc() {
      const map = this.tabHienTai === "bao-hanh"
        ? TEN_TRANG_THAI
        : TEN_TRANG_THAI;

      return Object.keys(map).map((value) => ({
        value,
        label: map[value],
      }));
    },

    danhSachBaoHanhLoc() {
      return this.locDanhSach(this.danhSachBaoHanh);
    },

    danhSachDoiTraLoc() {
      return this.locDanhSach(this.danhSachDoiTra);
    },

    baoHanhDangCho() {
      return this.danhSachBaoHanh.filter((x) =>
        !["HoanThanh", "TuChoi", "DaHuy"].includes(x.TrangThai)
      ).length;
    },

    doiTraDangCho() {
      return this.danhSachDoiTra.filter((x) =>
        !["HoanThanh", "TuChoi", "DaHuy"].includes(x.TrangThai)
      ).length;
    },

    trangThaiBaoHanhTiepTheo() {
      return this.baoHanhDangChon
        ? LUONG_BAO_HANH[this.baoHanhDangChon.TrangThai] || []
        : [];
    },

    trangThaiDoiTraTiepTheo() {
      return this.doiTraDangChon
        ? LUONG_DOI_TRA[this.doiTraDangChon.TrangThai] || []
        : [];
    },
  },

  mounted() {
    this.taiTatCa();
  },

  methods: {
    getConfig() {
      const token = localStorage.getItem("admin_token");

      if (!token) {
        throw new Error("Không tìm thấy token Admin. Vui lòng đăng nhập lại.");
      }

      return {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      };
    },

    locDanhSach(list) {
      const keyword = this.keyword.trim().toLowerCase();

      return list.filter((item) => {
        const dungTrangThai =
          !this.locTrangThai || item.TrangThai === this.locTrangThai;

        const dungLoai =
          this.tabHienTai !== "doi-tra" ||
          !this.locLoaiYeuCau ||
          item.LoaiYeuCau === this.locLoaiYeuCau;

        const khachHang = this.getKhachHang(item);
        const sanPham = this.getSanPham(item);

        const noiDung = [
          item.MaYCBH,
          item.MaYCDT,
          item.TinhTrangLoi,
          item.LyDoYeuCau,
          item.MoTaChiTiet,
          sanPham.TenSP,
          khachHang.HoTen,
          khachHang.Email,
          khachHang.SoDienThoai,
        ].join(" ").toLowerCase();

        return dungTrangThai && dungLoai && (!keyword || noiDung.includes(keyword));
      });
    },

    async taiTatCa() {
      this.dangTai = true;
      this.loi = "";

      await Promise.all([
        this.taiBaoHanh(),
        this.taiDoiTra(),
      ]);

      this.dangTai = false;
    },

    async taiLai() {
      await this.taiTatCa();
    },

    async taiBaoHanh() {
      try {
        const res = await axios.get(
          `${API}/admin/bao-hanh/get-data`,
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(res.data?.message || "Không tải được yêu cầu bảo hành.");
        }

        this.danhSachBaoHanh = Array.isArray(res.data.data) ? res.data.data : [];
      } catch (error) {
        console.error("Lỗi tải bảo hành:", error.response?.data || error);
        this.loi = error.response?.data?.message || error.message || "Không tải được dữ liệu bảo hành.";
      }
    },

    async taiDoiTra() {
      try {
        const res = await axios.get(
          `${API}/admin/doi-tra/get-data`,
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(res.data?.message || "Không tải được yêu cầu đổi trả.");
        }

        this.danhSachDoiTra = Array.isArray(res.data.data) ? res.data.data : [];
      } catch (error) {
        console.error("Lỗi tải đổi trả:", error.response?.data || error);
        this.loi = error.response?.data?.message || error.message || "Không tải được dữ liệu đổi trả.";
      }
    },

    async doiTab(tab) {
      this.tabHienTai = tab;
      this.keyword = "";
      this.locTrangThai = "";
      this.locLoaiYeuCau = "";

      if (tab === "bao-hanh" && !this.danhSachBaoHanh.length) {
        await this.taiBaoHanh();
      }

      if (tab === "doi-tra" && !this.danhSachDoiTra.length) {
        await this.taiDoiTra();
      }
    },

    locDuLieu() {
      // Danh sách đã được tải từ API; bộ lọc được áp dụng tại FE.
    },

    // Laravel thường serialize tên quan hệ theo snake_case.
    // Hỗ trợ cả snake_case và camelCase để tránh phụ thuộc cấu hình.
    getChiTietDonHang(item) {
      return item?.chiTietDonHang || item?.chi_tiet_don_hang || {};
    },

    getSanPham(item) {
      const chiTiet = this.getChiTietDonHang(item);
      return chiTiet?.sanPham || chiTiet?.san_pham || {};
    },

    getDonHang(item) {
      const chiTiet = this.getChiTietDonHang(item);
      return chiTiet?.donHang || chiTiet?.don_hang || {};
    },

    getKhachHang(item) {
      const donHang = this.getDonHang(item);
      return donHang?.nguoiDung || donHang?.nguoi_dung || {};
    },

    getSanPhamDoi(item) {
      return item?.sanPhamDoi || item?.san_pham_doi || {};
    },

    tenTrangThai(status) {
      return TEN_TRANG_THAI[status] || status || "Chưa xác định";
    },

    classTrangThai(status) {
      if (["HoanThanh", "DaHoanTien", "DaGiaoLaiKhach", "DaGiaoHangDoi", "DaThanhToan"].includes(status)) {
        return "status-success";
      }

      if (["TuChoi", "DaHuy", "KhachTuChoiBaoGia"].includes(status)) {
        return "status-danger";
      }

      if (["DangXuLy", "DangKiemTra", "DangBaoHanh", "DangVanChuyenVeShop", "DangVanChuyenTraKhach", "DangVanChuyenHangDoi"].includes(status)) {
        return "status-working";
      }

      return "status-waiting";
    },

    dinhDangTien(value) {
      if (value === null || value === undefined || value === "") return "—";
      return Number(value).toLocaleString("vi-VN") + " đ";
    },

    dinhDangNgay(value) {
      if (!value) return "—";

      const date = new Date(value);

      if (Number.isNaN(date.getTime())) return "—";

      return date.toLocaleString("vi-VN", {
        day: "2-digit",
        month: "2-digit",
        year: "numeric",
        hour: "2-digit",
        minute: "2-digit",
      });
    },

    layDanhSachAnh(value) {
      if (!value) return [];

      if (Array.isArray(value)) return value;

      if (typeof value === "string") {
        try {
          const parsed = JSON.parse(value);
          return Array.isArray(parsed) ? parsed : [];
        } catch {
          return [];
        }
      }

      return [];
    },

    async moChiTietBaoHanh(item) {
      this.hienModalBaoHanh = true;
      this.baoHanhDangChon = null;
      this.dangTaiChiTiet = true;
      this.formBaoGia = { ChiPhiDuKien: "", GhiChuBaoGia: "" };
      this.formBaoHanh = { TrangThai: "", LyDoTuChoi: "", KetQuaXuLy: "" };

      try {
        const res = await axios.get(
          `${API}/admin/bao-hanh/get-detail/${item.MaYCBH}`,
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(res.data?.message || "Không lấy được chi tiết bảo hành.");
        }

        this.baoHanhDangChon = res.data.data;
      } catch (error) {
        this.hienLoi(error, "Không lấy được chi tiết bảo hành.");
        this.hienModalBaoHanh = false;
      } finally {
        this.dangTaiChiTiet = false;
      }
    },

    async moChiTietDoiTra(item) {
      this.hienModalDoiTra = true;
      this.doiTraDangChon = null;
      this.dangTaiChiTiet = true;
      this.formDoiTra = { TrangThai: "", LyDoTuChoi: "" };
      this.formHoanTien = { SoTienHoan: "", PhuongThucHoanTien: "" };

      try {
        const res = await axios.get(
          `${API}/admin/doi-tra/get-detail/${item.MaYCDT}`,
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(res.data?.message || "Không lấy được chi tiết đổi trả.");
        }

        this.doiTraDangChon = res.data.data;
      } catch (error) {
        this.hienLoi(error, "Không lấy được chi tiết đổi trả.");
        this.hienModalDoiTra = false;
      } finally {
        this.dangTaiChiTiet = false;
      }
    },

    async lapBaoGia() {
      if (!this.baoHanhDangChon || this.dangXuLy) return;

      if (
        this.formBaoGia.ChiPhiDuKien === "" ||
        Number(this.formBaoGia.ChiPhiDuKien) < 0
      ) {
        alert("Vui lòng nhập chi phí sửa chữa hợp lệ.");
        return;
      }

      this.dangXuLy = true;

      try {
        const res = await axios.put(
          `${API}/admin/bao-hanh/lap-bao-gia`,
          {
            MaYCBH: this.baoHanhDangChon.MaYCBH,
            ChiPhiDuKien: Number(this.formBaoGia.ChiPhiDuKien),
            GhiChuBaoGia: this.formBaoGia.GhiChuBaoGia || null,
          },
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(res.data?.message || "Lập báo giá thất bại.");
        }

        alert(res.data.message || "Lập báo giá thành công.");
        await this.capNhatChiTietBaoHanh(res.data.data);
        await this.taiBaoHanh();
      } catch (error) {
        this.hienLoi(error, "Lập báo giá thất bại.");
      } finally {
        this.dangXuLy = false;
      }
    },

    async xacNhanThanhToan() {
      if (!this.baoHanhDangChon || this.dangXuLy) return;

      if (!confirm("Bạn đã kiểm tra và xác nhận shop thực sự nhận được khoản thanh toán này?")) {
        return;
      }

      this.dangXuLy = true;

      try {
        const res = await axios.put(
          `${API}/admin/bao-hanh/xac-nhan-thanh-toan`,
          { MaYCBH: this.baoHanhDangChon.MaYCBH },
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(res.data?.message || "Xác nhận thanh toán thất bại.");
        }

        alert(res.data.message || "Đã xác nhận thanh toán.");
        await this.capNhatChiTietBaoHanh(res.data.data);
        await this.taiBaoHanh();
      } catch (error) {
        this.hienLoi(error, "Xác nhận thanh toán thất bại.");
      } finally {
        this.dangXuLy = false;
      }
    },

    async capNhatTrangThaiBaoHanh() {
      if (!this.baoHanhDangChon || this.dangXuLy) return;

      const status = this.formBaoHanh.TrangThai;

      if (!status) {
        alert("Vui lòng chọn trạng thái tiếp theo.");
        return;
      }

      if (status === "TuChoi" && !this.formBaoHanh.LyDoTuChoi.trim()) {
        alert("Vui lòng nhập lý do từ chối.");
        return;
      }

      if (status === "HoanTatBaoHanh" && !this.formBaoHanh.KetQuaXuLy.trim()) {
        alert("Vui lòng nhập kết quả sửa chữa.");
        return;
      }

      this.dangXuLy = true;

      try {
        const res = await axios.put(
          `${API}/admin/bao-hanh/update-status`,
          {
            MaYCBH: this.baoHanhDangChon.MaYCBH,
            TrangThai: status,
            LyDoTuChoi: this.formBaoHanh.LyDoTuChoi || null,
            KetQuaXuLy: this.formBaoHanh.KetQuaXuLy || null,
          },
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(res.data?.message || "Cập nhật trạng thái thất bại.");
        }

        alert(res.data.message || "Cập nhật trạng thái thành công.");
        await this.capNhatChiTietBaoHanh(res.data.data);
        await this.taiBaoHanh();
        this.formBaoHanh = { TrangThai: "", LyDoTuChoi: "", KetQuaXuLy: "" };
      } catch (error) {
        this.hienLoi(error, "Cập nhật trạng thái thất bại.");
      } finally {
        this.dangXuLy = false;
      }
    },

    async capNhatChiTietBaoHanh(data) {
      if (data) {
        this.baoHanhDangChon = data;
      } else if (this.baoHanhDangChon) {
        const id = this.baoHanhDangChon.MaYCBH;
        const res = await axios.get(
          `${API}/admin/bao-hanh/get-detail/${id}`,
          this.getConfig()
        );
        if (res.data?.status) this.baoHanhDangChon = res.data.data;
      }
    },

    async capNhatTrangThaiDoiTra() {
      if (!this.doiTraDangChon || this.dangXuLy) return;

      const status = this.formDoiTra.TrangThai;

      if (!status) {
        alert("Vui lòng chọn trạng thái tiếp theo.");
        return;
      }

      if (status === "TuChoi" && !this.formDoiTra.LyDoTuChoi.trim()) {
        alert("Vui lòng nhập lý do từ chối.");
        return;
      }

      if (
        status === "DangVanChuyenHangDoi" &&
        !this.doiTraDangChon.MaSPDoi
      ) {
        alert("Yêu cầu chưa có mã sản phẩm đổi. Hãy kiểm tra dữ liệu yêu cầu trước khi giao hàng đổi.");
        return;
      }

      if (!confirm(`Bạn xác nhận chuyển yêu cầu sang "${this.tenTrangThai(status)}"?`)) {
        return;
      }

      this.dangXuLy = true;

      try {
        const res = await axios.put(
          `${API}/admin/doi-tra/update-status`,
          {
            MaYCDT: this.doiTraDangChon.MaYCDT,
            TrangThai: status,
            LyDoTuChoi: this.formDoiTra.LyDoTuChoi || null,
          },
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(res.data?.message || "Cập nhật trạng thái thất bại.");
        }

        alert(res.data.message || "Cập nhật trạng thái thành công.");
        this.doiTraDangChon = res.data.data;
        await this.taiDoiTra();
        this.formDoiTra = { TrangThai: "", LyDoTuChoi: "" };
      } catch (error) {
        this.hienLoi(error, "Cập nhật trạng thái thất bại.");
      } finally {
        this.dangXuLy = false;
      }
    },

    async xacNhanHoanTien() {
      if (!this.doiTraDangChon || this.dangXuLy) return;

      if (
        this.formHoanTien.SoTienHoan === "" ||
        Number(this.formHoanTien.SoTienHoan) < 0
      ) {
        alert("Vui lòng nhập số tiền hoàn hợp lệ.");
        return;
      }

      if (!this.formHoanTien.PhuongThucHoanTien) {
        alert("Vui lòng chọn phương thức hoàn tiền.");
        return;
      }

      if (!confirm("Bạn xác nhận đã thực hiện hoàn tiền cho khách hàng?")) {
        return;
      }

      this.dangXuLy = true;

      try {
        const res = await axios.put(
          `${API}/admin/doi-tra/xac-nhan-hoan-tien`,
          {
            MaYCDT: this.doiTraDangChon.MaYCDT,
            SoTienHoan: Number(this.formHoanTien.SoTienHoan),
            PhuongThucHoanTien: this.formHoanTien.PhuongThucHoanTien,
          },
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(res.data?.message || "Xác nhận hoàn tiền thất bại.");
        }

        alert(res.data.message || "Đã xác nhận hoàn tiền.");
        this.doiTraDangChon = res.data.data;
        await this.taiDoiTra();
      } catch (error) {
        this.hienLoi(error, "Xác nhận hoàn tiền thất bại.");
      } finally {
        this.dangXuLy = false;
      }
    },

    hienLoi(error, fallback) {
      console.error(fallback, error.response?.data || error);

      const errors = error.response?.data?.errors;
      const validationMessage = errors
        ? Object.values(errors).flat().join("\n")
        : "";

      alert(
        validationMessage ||
        error.response?.data?.message ||
        error.message ||
        fallback
      );
    },

    dongModal() {
      this.hienModalBaoHanh = false;
      this.hienModalDoiTra = false;
      this.baoHanhDangChon = null;
      this.doiTraDangChon = null;
    },
  },
};
</script>
<style scoped>
/* =========================================================
   BẢNG MÀU & BIẾN DÙNG CHUNG (giống trang Quản lý người dùng)
   ========================================================= */
.cs-page {
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
  --shadow-sm: 0 2px 8px rgba(15, 23, 42, 0.06);
  --ease: cubic-bezier(0.22, 1, 0.36, 1);

  width: 100%;
  min-width: 0;
  min-height: 100%;
  padding: 26px;
  color: var(--text);
  background: #f6f8fc;
  font-family: "Roboto", "Inter", Arial, sans-serif;
}

.cs-page * {
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

@keyframes pop {
  from { opacity: 0; transform: translateY(14px) scale(0.97); }
  to { opacity: 1; transform: translateY(0) scale(1); }
}

@keyframes shine {
  from { transform: translateX(-120%) skewX(-20deg); }
  to { transform: translateX(260%) skewX(-20deg); }
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.cs-heading { animation: fadeUp 0.5s var(--ease) both; }

.stat-card { animation: fadeUp 0.55s var(--ease) both; }
.stat-card:nth-child(1) { animation-delay: 0.05s; }
.stat-card:nth-child(2) { animation-delay: 0.12s; }
.stat-card:nth-child(3) { animation-delay: 0.19s; }
.stat-card:nth-child(4) { animation-delay: 0.26s; }

.cs-tabs { animation: fadeUp 0.55s var(--ease) 0.3s both; }
.cs-toolbar { animation: fadeUp 0.55s var(--ease) 0.34s both; }
.cs-panel { animation: fadeUp 0.6s var(--ease) 0.38s both; }

.cs-table tbody tr { animation: rowIn 0.4s var(--ease) both; }
.cs-table tbody tr:nth-child(1) { animation-delay: 0.04s; }
.cs-table tbody tr:nth-child(2) { animation-delay: 0.08s; }
.cs-table tbody tr:nth-child(3) { animation-delay: 0.12s; }
.cs-table tbody tr:nth-child(4) { animation-delay: 0.16s; }
.cs-table tbody tr:nth-child(5) { animation-delay: 0.2s; }
.cs-table tbody tr:nth-child(6) { animation-delay: 0.24s; }
.cs-table tbody tr:nth-child(7) { animation-delay: 0.28s; }
.cs-table tbody tr:nth-child(8) { animation-delay: 0.32s; }

/* =========================================================
   TIÊU ĐỀ
   ========================================================= */
.cs-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 26px;
}

.cs-eyebrow {
  display: inline-flex;
  margin-bottom: 10px;
  padding: 4px 12px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 1px;
}

.cs-heading h1 {
  margin: 0;
  font-size: 29px;
  font-weight: 800;
  letter-spacing: -0.4px;
  background: linear-gradient(90deg, #0f172a 0%, #4f46e5 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.cs-heading p {
  margin: 8px 0 0;
  color: var(--muted);
  font-size: 14px;
  line-height: 1.6;
}

/* =========================================================
   NÚT
   ========================================================= */
.btn-refresh,
.btn-filter,
.btn-detail,
.btn-primary,
.btn-secondary {
  position: relative;
  overflow: hidden;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  min-height: 42px;
  padding: 0 18px;
  border: 0;
  border-radius: 11px;
  font-size: 13px;
  font-weight: 700;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease),
    background 0.25s, border-color 0.25s, color 0.25s;
}

.btn-refresh,
.btn-filter,
.btn-primary {
  color: #fff;
  background: var(--grad-primary);
  background-size: 140% 140%;
  background-position: 0% 50%;
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.32);
}

.btn-refresh::after,
.btn-filter::after,
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

.btn-refresh:hover:not(:disabled),
.btn-filter:hover:not(:disabled),
.btn-primary:hover:not(:disabled) {
  transform: translateY(-2px);
  background-position: 100% 50%;
  box-shadow: 0 12px 26px rgba(79, 70, 229, 0.42);
}

.btn-refresh:hover:not(:disabled)::after,
.btn-filter:hover:not(:disabled)::after,
.btn-primary:hover:not(:disabled)::after {
  animation: shine 0.8s var(--ease);
}

.btn-refresh:active:not(:disabled),
.btn-filter:active:not(:disabled),
.btn-primary:active:not(:disabled) {
  transform: scale(0.97);
}

.btn-refresh:disabled,
.btn-primary:disabled {
  opacity: 0.7;
  cursor: wait;
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

.btn-detail {
  min-height: 34px;
  padding: 0 13px;
  font-size: 11px;
  color: #fff;
  white-space: nowrap;
  background: linear-gradient(135deg, #6366f1, #2563eb);
  box-shadow: 0 5px 12px rgba(79, 70, 229, 0.3);
}

.btn-detail:hover {
  transform: translateY(-3px);
  filter: brightness(1.06);
  box-shadow: 0 10px 20px rgba(79, 70, 229, 0.45);
}

.btn-detail:active {
  transform: scale(0.95);
}

/* =========================================================
   THẺ THỐNG KÊ
   ========================================================= */
.cs-stats {
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
  min-height: 104px;
  padding: 20px;
  border: 1px solid var(--line);
  border-radius: 16px;
  background: #fff;
  box-shadow: var(--shadow-sm);
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease), border-color 0.3s;
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
.stat-card:nth-child(2) { --tone: #f59e0b; --tone-soft: rgba(245, 158, 11, 0.13); }
.stat-card:nth-child(3) { --tone: #7c3aed; --tone-soft: rgba(124, 58, 237, 0.11); }
.stat-card:nth-child(4) { --tone: #10b981; --tone-soft: rgba(16, 185, 129, 0.12); }

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
  font-size: 22px;
  transition: transform 0.35s var(--ease);
}

.stat-card:hover .stat-icon {
  transform: rotate(-8deg) scale(1.1);
}

.stat-icon.blue {
  color: #fff;
  background: linear-gradient(135deg, #6366f1, #4f46e5);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.35);
}

.stat-icon.orange {
  color: #fff;
  background: linear-gradient(135deg, #fbbf24, #f97316);
  box-shadow: 0 8px 16px rgba(245, 158, 11, 0.38);
}

.stat-icon.purple {
  color: #fff;
  background: linear-gradient(135deg, #a78bfa, #7c3aed);
  box-shadow: 0 8px 16px rgba(124, 58, 237, 0.35);
}

.stat-icon.green {
  color: #fff;
  background: linear-gradient(135deg, #34d399, #059669);
  box-shadow: 0 8px 16px rgba(16, 185, 129, 0.35);
}

.stat-card span {
  display: block;
  margin-bottom: 6px;
  color: var(--muted);
  font-size: 12px;
  font-weight: 600;
}

.stat-card strong {
  color: var(--navy);
  font-size: 28px;
  font-weight: 800;
  line-height: 1.1;
}

/* =========================================================
   TABS
   ========================================================= */
.cs-tabs {
  display: flex;
  gap: 8px;
  width: fit-content;
  max-width: 100%;
  margin-bottom: 16px;
  padding: 0;
  background: transparent;
}

.cs-tabs button {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  padding: 11px 18px;
  border: 1.5px solid var(--line);
  border-radius: 11px;
  color: var(--muted);
  background: #fff;
  cursor: pointer;
  font-size: 12.5px;
  font-weight: 700;
  transition: all 0.28s var(--ease);
}

.cs-tabs button:hover:not(.active) {
  color: var(--primary);
  border-color: #c7d2fe;
  background: #eef2ff;
  transform: translateY(-2px);
}

.cs-tabs button.active {
  border-color: transparent;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.35);
}

.cs-tabs button span {
  display: inline-block;
  min-width: 24px;
  margin-left: 0;
  padding: 3px 8px;
  height: auto;
  border-radius: 999px;
  color: inherit;
  background: rgba(100, 116, 139, 0.14);
  text-align: center;
  font-size: 11px;
  font-weight: 800;
}

.cs-tabs button.active span {
  background: rgba(255, 255, 255, 0.25);
}

/* =========================================================
   THANH TÌM KIẾM & LỌC
   ========================================================= */
.cs-toolbar {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 16px;
  padding: 14px;
  border: 1px solid var(--line);
  border-radius: 14px;
  background: #fff;
  box-shadow: var(--shadow-sm);
}

.cs-search {
  position: relative;
  flex: 1;
  min-width: 220px;
}

.cs-search > i {
  position: absolute;
  left: 14px;
  top: 50%;
  transform: translateY(-50%);
  color: #94a3b8;
}

.cs-search input,
.cs-toolbar select {
  height: 42px;
  border: 1.5px solid var(--line);
  border-radius: 10px;
  color: #334155;
  background: #f8fafc;
  font-size: 12.5px;
  outline: 0;
  transition: all 0.25s var(--ease);
}

.cs-search input {
  width: 100%;
  padding: 0 38px;
}

.cs-toolbar select {
  min-width: 160px;
  padding: 0 10px;
  font-weight: 600;
  cursor: pointer;
}

.cs-search input:hover,
.cs-toolbar select:hover {
  border-color: #c7d2fe;
}

.cs-search input:focus,
.cs-toolbar select:focus,
.action-box input:focus,
.action-box textarea:focus,
.action-box select:focus {
  border-color: var(--primary);
  background: #fff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}

.cs-search button {
  position: absolute;
  right: 9px;
  top: 50%;
  transform: translateY(-50%);
  border: 0;
  color: var(--muted);
  background: transparent;
  cursor: pointer;
}

.cs-search button:hover {
  color: #e11d48;
}

/* =========================================================
   KHUNG DANH SÁCH
   ========================================================= */
.cs-panel {
  overflow: hidden;
  border: 1px solid var(--line);
  border-radius: 18px;
  background: #fff;
  box-shadow: 0 8px 30px rgba(15, 23, 42, 0.06);
}

.panel-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  padding: 22px 26px;
  border-bottom: 1px solid var(--line);
}

.panel-heading h3 {
  margin: 0;
  color: var(--navy);
  font-size: 18px;
  font-weight: 800;
}

.panel-heading p {
  margin: 7px 0 0;
  color: var(--muted);
  font-size: 12.5px;
}

.total-label {
  flex-shrink: 0;
  padding: 8px 14px;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
  font-size: 12px;
  font-weight: 700;
}

/* =========================================================
   BẢNG
   ========================================================= */
.table-responsive {
  width: 100%;
  overflow-x: auto;
}

.cs-table {
  width: 100%;
  min-width: 1000px;
  border-collapse: separate;
  border-spacing: 0;
  text-align: left;
}

.cs-table th {
  padding: 15px 13px;
  color: #3730a3;
  background: linear-gradient(180deg, #eef2ff, #e0e7ff);
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 0.4px;
  text-align: left;
  text-transform: uppercase;
  white-space: nowrap;
}

.cs-table td {
  padding: 14px 13px;
  border-top: 0;
  border-bottom: 1px solid #eef2f7;
  color: #475569;
  font-size: 12px;
  vertical-align: middle;
  transition: background 0.25s;
}

.cs-table th:first-child,
.cs-table td:first-child { padding-left: 24px; }

.cs-table th:last-child,
.cs-table td:last-child { padding-right: 22px; }

.cs-table tbody tr:hover td { background: #f5f7ff; }
.cs-table tbody tr:hover td:first-child { box-shadow: inset 4px 0 0 var(--primary); }
.cs-table tbody tr:last-child td { border-bottom: 0; }

.request-code {
  display: inline-flex;
  padding: 6px 10px;
  border-radius: 8px;
  color: #4338ca;
  background: #e0e7ff;
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 0.3px;
}

.customer-cell strong,
.customer-cell small,
.product-cell-info strong,
.product-cell-info small {
  display: block;
}

.customer-cell strong,
.product-cell-info strong {
  color: var(--navy);
  font-size: 12.5px;
  font-weight: 700;
  transition: color 0.2s;
}

.cs-table tbody tr:hover .customer-cell strong,
.cs-table tbody tr:hover .product-cell-info strong {
  color: var(--primary);
}

.customer-cell small,
.product-cell-info small {
  margin-top: 4px;
  color: #94a3b8;
  font-size: 11px;
}

.product-cell {
  display: flex;
  align-items: center;
  gap: 11px;
  min-width: 190px;
}

.product-cell img {
  width: 44px;
  height: 44px;
  object-fit: contain;
  border: 2px solid #fff;
  border-radius: 12px;
  background: #f8fafc;
  box-shadow: 0 0 0 2px #c7d2fe, 0 4px 10px rgba(79, 70, 229, 0.15);
  transition: transform 0.3s var(--ease);
}

.cs-table tbody tr:hover .product-cell img {
  transform: scale(1.1);
}

.money-cell {
  color: #d97706;
  font-weight: 800;
  white-space: nowrap;
}

/* =========================================================
   PILL TRẠNG THÁI & LOẠI YÊU CẦU
   ========================================================= */
.status-pill,
.type-pill {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 6px 12px;
  border-radius: 999px;
  font-size: 11px;
  font-weight: 700;
  white-space: nowrap;
}

.status-pill::before {
  content: "";
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: currentColor;
}

.status-waiting { color: #b45309; background: #fef3c7; }
.status-working { color: #3730a3; background: #e0e7ff; }
.status-success { color: #047857; background: #d1fae5; }
.status-danger { color: #be123c; background: #ffe4e6; }

.type-exchange { color: #0369a1; background: #e0f2fe; }
.type-refund { color: #6d28d9; background: #ede9fe; }

/* =========================================================
   TRỐNG / ĐANG TẢI / LỖI
   ========================================================= */
.empty-state,
.loading-state {
  min-height: 200px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 9px;
  padding: 30px 20px;
  color: var(--muted);
  font-size: 12.5px;
  text-align: center;
}

.empty-state > i {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 72px;
  height: 72px;
  margin-bottom: 6px;
  border-radius: 50%;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 28px;
}

.empty-state strong {
  color: var(--navy);
  font-size: 15px;
  font-weight: 800;
}

.loading-state > i {
  color: var(--primary);
  font-size: 22px;
}

.cs-error {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 16px;
  padding: 13px 16px;
  border: 1px solid #fecdd3;
  border-radius: 12px;
  color: #be123c;
  background: linear-gradient(180deg, #fff1f2, #fff);
  font-size: 12.5px;
  font-weight: 600;
}

/* =========================================================
   MODAL
   ========================================================= */
.cs-modal-backdrop {
  position: fixed;
  z-index: 3000;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 24px;
  overflow-y: auto;
  background: rgba(15, 23, 42, 0.6);
  backdrop-filter: blur(5px);
  animation: fadeUp 0.25s var(--ease) both;
}

.cs-modal {
  width: min(900px, 100%);
  max-height: calc(100vh - 48px);
  overflow-y: auto;
  padding: 26px;
  border-radius: 20px;
  background: #f8fafc;
  box-shadow: 0 30px 80px rgba(0, 0, 0, 0.3);
  animation: pop 0.35s var(--ease) both;
}

.modal-heading {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 15px;
  margin-bottom: 18px;
}

.modal-heading h2 {
  margin: 0;
  font-size: 22px;
  font-weight: 800;
  letter-spacing: -0.3px;
  background: linear-gradient(90deg, #0f172a 0%, #4f46e5 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.modal-close {
  width: 36px;
  height: 36px;
  border: 0;
  border-radius: 10px;
  color: #475569;
  background: #e9eef5;
  cursor: pointer;
  transition: all 0.25s var(--ease);
}

.modal-close:hover {
  color: #fff;
  background: var(--grad-danger);
  transform: rotate(90deg);
}

.detail-status-row {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 9px;
  margin-bottom: 16px;
}

.detail-date {
  margin-left: auto;
  color: var(--muted);
  font-size: 11.5px;
  font-weight: 600;
}

.detail-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  margin-bottom: 12px;
}

.detail-box {
  min-width: 0;
  padding: 18px;
  border: 1px solid var(--line);
  border-radius: 14px;
  background: #fff;
  box-shadow: var(--shadow-sm);
}

.detail-box h4,
.action-box h4 {
  display: flex;
  align-items: center;
  gap: 8px;
  margin: 0 0 13px;
  color: var(--navy);
  font-size: 13.5px;
  font-weight: 800;
}

.detail-box h4 i,
.action-box h4 i {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  margin-right: 0;
  border-radius: 8px;
  color: #fff;
  background: var(--grad-primary);
  font-size: 12px;
}

.detail-box p {
  margin: 7px 0;
  color: var(--muted);
  font-size: 12px;
  line-height: 1.6;
  overflow-wrap: anywhere;
}

.detail-box p b {
  color: #334155;
}

.full-width { margin-bottom: 12px; }

.detail-product {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 12px;
}

.detail-product img {
  width: 56px;
  height: 56px;
  object-fit: contain;
  border: 2px solid #fff;
  border-radius: 14px;
  background: #f8fafc;
  box-shadow: 0 0 0 2px #c7d2fe, 0 4px 10px rgba(79, 70, 229, 0.18);
}

.detail-product strong,
.detail-product small {
  display: block;
  font-size: 12.5px;
}

.detail-product strong { color: var(--navy); font-weight: 700; }
.detail-product small { margin-top: 5px; color: #94a3b8; font-size: 11px; }

.description-text {
  white-space: pre-line;
  color: #334155 !important;
}

.evidence-list {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 12px;
}

.evidence-list a {
  display: block;
  transition: transform 0.3s var(--ease);
}

.evidence-list a:hover { transform: translateY(-4px) scale(1.04); }

.evidence-list img {
  width: 84px;
  height: 84px;
  object-fit: cover;
  border: 2px solid #fff;
  border-radius: 12px;
  box-shadow: 0 0 0 2px #c7d2fe, 0 4px 10px rgba(79, 70, 229, 0.15);
}

.quote-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
  margin: 12px 0;
}

.quote-grid > div {
  padding: 13px;
  border: 1px solid var(--line);
  border-radius: 12px;
  background: linear-gradient(180deg, #f8fafc, #fff);
}

.quote-grid span,
.quote-grid strong { display: block; }

.quote-grid span {
  margin-bottom: 6px;
  color: var(--muted);
  font-size: 11px;
  font-weight: 600;
}

.quote-grid strong {
  color: var(--navy);
  font-size: 14px;
  font-weight: 800;
}

.money-green { color: #059669 !important; }
.money-orange { color: #d97706 !important; }
.text-danger { color: #be123c !important; font-weight: 600; }

/* =========================================================
   KHUNG THAO TÁC
   ========================================================= */
.action-box {
  margin-top: 13px;
  padding: 18px;
  border: 1px solid #c7d2fe;
  border-radius: 14px;
  background: linear-gradient(180deg, #eef2ff, #f8faff);
}

.payment-box {
  border-color: #a7f3d0;
  background: linear-gradient(180deg, #ecfdf5, #f7fffb);
}

.payment-box h4 i {
  background: var(--grad-success);
}

.action-box > p {
  margin: 0 0 8px;
  color: var(--muted);
  font-size: 12px;
  line-height: 1.6;
}

.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

.action-box label {
  display: block;
  margin: 12px 0;
  color: #475569;
  font-size: 11.5px;
  font-weight: 700;
}

.action-box input,
.action-box textarea,
.action-box select {
  display: block;
  width: 100%;
  margin-top: 7px;
  padding: 10px 12px;
  border: 1.5px solid var(--line);
  border-radius: 10px;
  color: #334155;
  background: #fff;
  font-family: inherit;
  font-size: 12.5px;
  outline: 0;
  resize: vertical;
  transition: all 0.25s var(--ease);
}

.action-box .btn-primary {
  margin-top: 8px;
}

.warning-note {
  display: flex;
  align-items: flex-start;
  gap: 8px;
  padding: 11px 13px;
  border: 1px solid #fed7aa;
  border-radius: 10px;
  color: #c2410c !important;
  background: #fff7ed;
  font-size: 12px;
  line-height: 1.5;
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  margin-top: 18px;
}

.spinning {
  animation: spin 1s linear infinite;
}

/* Thanh cuộn gọn */
.table-responsive::-webkit-scrollbar,
.cs-modal::-webkit-scrollbar { height: 8px; width: 8px; }

.table-responsive::-webkit-scrollbar-thumb,
.cs-modal::-webkit-scrollbar-thumb {
  border-radius: 8px;
  background: #c7d2fe;
}

.table-responsive::-webkit-scrollbar-thumb:hover,
.cs-modal::-webkit-scrollbar-thumb:hover { background: #818cf8; }

/* =========================================================
   RESPONSIVE
   ========================================================= */
@media (max-width: 1000px) {
  .cs-stats { grid-template-columns: repeat(2, minmax(0, 1fr)); }
  .cs-toolbar { flex-wrap: wrap; }
  .cs-search { flex-basis: 100%; }
}

@media (max-width: 650px) {
  .cs-page { padding: 14px; }

  .cs-heading {
    align-items: flex-start;
    flex-direction: column;
  }

  .cs-heading h1 { font-size: 24px; }

  .cs-stats { grid-template-columns: 1fr; gap: 10px; }

  .cs-tabs { width: 100%; }
  .cs-tabs button { flex: 1; justify-content: space-between; padding: 10px 12px; }

  .panel-heading { align-items: flex-start; padding: 18px 16px; }

  .detail-grid,
  .form-grid { grid-template-columns: 1fr; }

  .quote-grid { grid-template-columns: repeat(2, 1fr); }

  .cs-modal-backdrop { padding: 9px; }
  .cs-modal { padding: 15px; max-height: calc(100vh - 18px); }

  .detail-date { width: 100%; margin-left: 0; }
}

/* Tôn trọng người dùng tắt hiệu ứng chuyển động */
@media (prefers-reduced-motion: reduce) {
  .cs-page *,
  .cs-page *::before,
  .cs-page *::after {
    animation: none !important;
    transition: none !important;
  }
}
</style>