<template>
  <div class="home-page">
    <!-- ===================================================== -->
    <!-- HERO -->
    <!-- ===================================================== -->

    <section class="container hero-zone">
      <div class="hero-layout">
        <!-- HERO MAIN -->
        <div class="hero-main">
          <div class="hero-image-wrap">
            <img
              src="https://images.pexels.com/photos/16230157/pexels-photo-16230157.jpeg?cs=srgb&dl=pexels-nikhil-soni-159587696-16230157.jpg&fm=jpg"
              
              class="hero-main-image"
              alt="TechShop"
            />

            <div class="hero-shade"></div>

            <div class="hero-content">
              <div class="hero-badge">
                <span class="pulse-dot"></span>
                TECHSHOP GAMING STORE
              </div>

              <h1>
                POWER UP
                <span>YOUR SETUP</span>
              </h1>

              <p>Laptop • PC Gaming • Monitor • Gear</p>

              <div class="hero-actions">
                <button class="hero-btn" @click="$router.push('/ve-chung-toi')">
                  KHÁM PHÁ NGAY
                  <i class="fa-solid fa-arrow-right"></i>
                </button>

                <div class="hero-mini-stat">
                  <strong>2026</strong>
                  <span>NEW GEN</span>
                </div>
              </div>
            </div>

            <div class="hero-floating-chip chip-one">
              <i class="fa-solid fa-bolt"></i>
              PERFORMANCE
            </div>

            <div class="hero-floating-chip chip-two">
              <i class="fa-solid fa-microchip"></i>
              HIGH END
            </div>
          </div>
        </div>

        <!-- HERO SIDE -->
        <div class="hero-side">
          <div class="hero-promo-card">
            <img src="/public/img_trangchu/banner.jpg" alt="" />

            <div class="promo-shine"></div>
          </div>

          <div class="hero-promo-card">
            <img src="/public/img_trangchu/trangchu_dai2.png" alt="" />

            <div class="promo-shine"></div>
          </div>

          <div class="hero-promo-card">
            <img src="/public/img_trangchu/trangchu_dai1.png" alt="" />

            <div class="promo-shine"></div>
          </div>
        </div>
      </div>
    </section>

    <!-- ===================================================== -->
    <!-- SERVICE STRIP -->
    <!-- ===================================================== -->

    
<section class="container service-section">
  <div class="service-section-heading">
    <div>
      <span class="service-kicker">
        <span></span>
        TECHSHOP EXPERIENCE
      </span>
      <h2>Dịch vụ <span>của chúng tôi</span></h2>
    </div>

    <p>
      Đồng hành cùng bạn trong từng trải nghiệm mua sắm công nghệ.
    </p>
  </div>

  <div class="service-strip">

    <!-- GIAO HÀNG -->
    <article class="service-card service-delivery">
      <div class="service-card-top">
        <div class="service-icon">
          <i class="fa-solid fa-truck-fast"></i>
        </div>
        <span class="service-number">01</span>
      </div>

      <div class="service-copy">
        <strong>Giao hàng nhanh</strong>
        <span>Giao tận nơi trên toàn quốc</span>
      </div>

      <div class="service-card-bottom">
        <span>FAST DELIVERY</span>
        <i class="fa-solid fa-arrow-right"></i>
      </div>
    </article>

    <!-- BẢO HÀNH -->
    <article class="service-card service-warranty">
      <div class="service-card-top">
        <div class="service-icon">
          <i class="fa-solid fa-shield-halved"></i>
        </div>
        <span class="service-number">02</span>
      </div>

      <div class="service-copy">
        <strong>Bảo hành chính hãng</strong>
        <span>An tâm trong suốt quá trình sử dụng</span>
      </div>

      <div class="service-card-bottom">
        <span>OFFICIAL WARRANTY</span>
        <i class="fa-solid fa-arrow-right"></i>
      </div>
    </article>

    <!-- HỖ TRỢ -->
    <article class="service-card service-support">
      <div class="service-card-top">
        <div class="service-icon">
          <i class="fa-solid fa-headset"></i>
        </div>
        <span class="service-number">03</span>
      </div>

      <div class="service-copy">
        <strong>Hỗ trợ tận tâm</strong>
        <span>TechShop AI luôn sẵn sàng hỗ trợ</span>
      </div>

      <div class="service-card-bottom">
        <span>SMART SUPPORT</span>
        <i class="fa-solid fa-arrow-right"></i>
      </div>
    </article>

    <!-- DEAL -->
    <article class="service-card service-deal">
      <div class="service-card-top">
        <div class="service-icon">
          <i class="fa-solid fa-bolt"></i>
        </div>
        <span class="service-number">04</span>
      </div>

      <div class="service-copy">
        <strong>Ưu đãi Gaming</strong>
        <span>Cập nhật nhiều chương trình hấp dẫn</span>
      </div>

      <div class="service-card-bottom">
        <span>EXCLUSIVE DEALS</span>
        <i class="fa-solid fa-arrow-right"></i>
      </div>
    </article>

  </div>
</section>


    <!-- ===================================================== -->
    <!-- CATEGORY + PROMO -->
    <!-- ===================================================== -->

    <section class="container category-section">
      <div class="category-layout">
        <!-- CATEGORY -->
        <div class="category-panel">
          <div class="category-header">
            <div class="category-header-icon">
              <i class="fa-solid fa-layer-group"></i>
            </div>

            <div>
              <span>EXPLORE</span>
              <h3>DANH MỤC</h3>
            </div>
          </div>

          <div class="category-list">

<!-- TẤT CẢ SẢN PHẨM -->
<button
  type="button"
  class="category-item"
  :class="{ active: selectedCategory === 'all' }"
  @click="chonDanhMuc('all')"
>
  <div class="category-left">
    <i class="fa-solid fa-border-all"></i>
    <span>Tất cả sản phẩm</span>
  </div>

  <i class="fa-solid fa-angle-right"></i>
</button>

<!-- DANH MỤC LẤY TỪ BACKEND -->
<button
  v-for="dm in categories"
  :key="dm.MaDM"
  type="button"
  class="category-item"
  :class="{
    active: Number(selectedCategory) === Number(dm.MaDM)
  }"
  @click="chonDanhMuc(dm.MaDM)"
>
  <div class="category-left">
    <i :class="iconDanhMuc(dm.TenDM)"></i>
    <span>{{ dm.TenDM }}</span>
  </div>

 

  <i class="fa-solid fa-angle-right"></i>
</button>

</div>
        </div>

        <!-- PROMO -->
        <div class="promo-area">
          <div class="promo-big">
            <img src="/public/img_trangchu/vuong.jpg" alt="" />

            <div class="promo-overlay">
              <div class="promo-tag">TECHSHOP DEAL</div>

              <h2>
                LEVEL UP
                <span>YOUR GEAR</span>
              </h2>

              <p>Gaming setup mới bắt đầu từ đây.</p>
            </div>
          </div>

          
<div class="promo-small-row">
  <div class="promo-small">
    <img
      src="https://static.wixstatic.com/media/fcd6e5_1c53cb4b04d140d783ee6b5c7b364d31~mv2.jpg/v1/fill/w_980%2Ch_545%2Cal_c%2Cq_85%2Cusm_0.66_1.00_0.01%2Cenc_auto/fcd6e5_1c53cb4b04d140d783ee6b5c7b364d31~mv2.jpg"
      alt="PC Gaming RGB"
    />
  </div>

  <div class="promo-small">
    <img
      src="https://cdn.mos.cms.futurecdn.net/ky5SNRNR255FUbDuW6Ji4E-970-80.jpg"
      alt="Bàn phím cơ Gaming RGB"
    />
  </div>

  <div class="promo-small">
    <img
      src="https://assets3.razerzone.com/Xe9kRTiWfFvh6I2ZIRX4wGMfzPk%3D/1199x799/https%3A%2F%2Fmedias-p1.phoenix.razer.com%2Fsys-master-phoenix-images-container%2Fhe8%2Fh16%2F9807221981214%2F240820-kraken-v4-1500x1000-2.jpg"
      alt="Tai nghe Razer Gaming"
    />
  </div>
</div>

        </div>
      </div>
    </section>

    <!-- ===================================================== -->
    <!-- 3D SHOWROOM -->
    <!-- ===================================================== -->

    <section class="container showroom-section">
      <div class="showroom">
        <div class="showroom-grid"></div>

        <div class="showroom-glow glow-left"></div>
        <div class="showroom-glow glow-right"></div>

        <!-- LEFT CONTENT -->
        <div class="showroom-info">
          <div class="showroom-eyebrow">
            <span></span>
            TECHSHOP 3D SHOWROOM
          </div>

          <h2>
            BUILD YOUR
            <span>DREAM PC</span>
          </h2>

          <p>
            Khám phá những bộ PC Gaming nổi bật của TechShop trong không gian 3D
            giả lập.
          </p>

          <div class="showroom-tags">
            <span>
              <i class="fa-solid fa-microchip"></i>
              RTX SERIES
            </span>

            <span>
              <i class="fa-solid fa-memory"></i>
              DDR5
            </span>

            <span>
              <i class="fa-solid fa-gauge-high"></i>
              HIGH FPS
            </span>
          </div>

          <div class="showroom-line"></div>

          <div class="showroom-note">
            <div class="showroom-note-icon">
              <i class="fa-solid fa-cube"></i>
            </div>

            <div>
              <strong>3D PRODUCT SHOWCASE</strong>
              <span> Di chuột để tương tác </span>
            </div>
          </div>
        </div>

        <!-- RIGHT 3D -->
        <div class="showroom-scene">
          <div class="scene-platform"></div>

          <div class="scene-ring ring-one"></div>
          <div class="scene-ring ring-two"></div>

          <div class="pc-orbit">
            <div
              v-for="(item, index) in pcProducts"
              :key="index"
              class="pc-3d-card"
              :style="{ '--index': index }"
            >
              <div class="pc-3d-inner">
                <div class="pc-card-top">
                  <span class="pc-label"> GAMING PC </span>

                  <span class="pc-number"> 0{{ index + 1 }} </span>
                </div>

                <div class="pc-image-wrap">
                  <div class="image-glow"></div>

                  <img :src="item.hinh" :alt="item.ten" />
                </div>

                <div class="pc-card-content">
                  <h4>
                    {{ item.ten }}
                  </h4>

                  <p>
                    {{ item.chitiet }}
                  </p>

                  <div class="pc-bottom">
                    <strong> {{ Number(item.gia).toLocaleString() }}đ </strong>

                    <span> ⭐ {{ item.danhgia }} </span>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- CORE -->
          <div class="showroom-core">
            <div class="core-ring ring-a"></div>
            <div class="core-ring ring-b"></div>

            <div class="core-center">
              <i class="fa-solid fa-cube"></i>

              <span> TECH </span>
            </div>
          </div>

          <div class="scene-shadow"></div>
        </div>
      </div>
    </section>

    <!-- ===================================================== -->
    <!-- API PRODUCT -->
    <!-- ===================================================== -->

    <section class="container product-section-wrap">
      <div class="section-head-modern">
        <div>
          <span class="section-kicker"> TECHSHOP STORE </span>

          <h2>
            SẢN PHẨM
            <span>NỔI BẬT</span>
          </h2>

          <p>Những sản phẩm đang có tại cửa hàng</p>
        </div>

        <button
          type="button"
          class="section-link section-link-button"
          @click="toggleShowAllProducts"
          :aria-expanded="showAllProducts"
        >
          {{ showAllProducts ? "THU GỌN" : "XEM TẤT CẢ" }}
          <i
            class="fa-solid"
            :class="showAllProducts ? 'fa-arrow-up' : 'fa-arrow-right'"
          ></i>
        </button>
      </div>

      <div class="modern-product-grid">
        <div
          v-for="item in displayProducts"
          :key="item.MaSP"
          class="modern-product-card"
          @click="clickProduct(item)"
          @mousemove="tiltProductCard($event)"
          @mouseleave="resetProductCard($event)"
        >
          <div class="product-top">
            <span class="product-code"> #{{ item.MaSP }} </span>

            <span
              class="status-pill"
              :class="{
                'status-active':
                  item.TrangThaiSP === 'DangBan' && Number(item.SoLuongTon) > 0,
                'status-off':
                  item.TrangThaiSP === 'NgungBan' ||
                  Number(item.SoLuongTon) <= 0,
              }"
            >
              {{
                item.TrangThaiSP === "NgungBan"
                  ? "NGỪNG KINH DOANH"
                  : Number(item.SoLuongTon) > 0
                  ? "ĐANG BÁN"
                  : "HẾT HÀNG"
              }}
            </span>
          </div>

          <div class="product-image-box">
            <div class="product-image-glow"></div>

            <img :src="item.HinhAnh" :alt="item.TenSP" />

            <!-- Nhãn bảo hành ở góc ảnh -->
            <span
              v-if="Number(item.ThoiHanBaoHanhThang) > 0"
              class="product-warranty-badge"
            >
              <i class="fa-solid fa-shield-halved"></i>
              Bảo hành {{ item.ThoiHanBaoHanhThang }} tháng
            </span>

            <div class="floating-like">
              <i class="fa-regular fa-heart"></i>
            </div>
          </div>

          <div class="product-info">
                <div class="product-brand">
      <img
        v-if="item.AnhThuongHieu"
        :src="item.AnhThuongHieu"
        :alt="item.ThuongHieu || 'Thương hiệu'"
        class="product-brand-logo"
      />

      <span>{{ item.ThuongHieu || "TECHSHOP" }}</span>
    </div>

            <h3>
              {{ item.TenSP }}
            </h3>

            <div class="product-meta">
              <span>
                Tồn kho:
                <b>{{ item.SoLuongTon }}</b>
              </span>

              <span>
                ID:
                <b>{{ item.MaSP }}</b>
              </span>
            </div>

            <div class="product-pricing">
              <span class="price-label"> Giá bán </span>

              <strong> {{ Number(item.GiaBan).toLocaleString() }}đ </strong>
            </div>

            <button class="product-cart-btn" @click.stop="themGioHang(item)">
              <span> THÊM VÀO GIỎ </span>

              <i class="fa-solid fa-cart-plus"></i>
            </button>
          </div>
        </div>
      </div>
    </section>

    <!-- ===================================================== -->
    <!-- SECOND BANNER -->
    <!-- ===================================================== -->

    <section class="container banner-row-section">
      <div class="banner-card-modern">
        <img src="/public/img_trangchu/tren1.png" alt="" /> 

        <div class="banner-shine"></div>
      </div>

      <div class="banner-card-modern">
        <img src="/public/img_trangchu/tren2.png" alt="" />

        <div class="banner-shine"></div>
      </div>

      <div class="banner-card-modern">
        <img src="/public/img_trangchu/trangchu_dai2.png" alt="" />

        <div class="banner-shine"></div>
      </div>
    </section>

    <!-- PC GAMING đã được giới thiệu trong khu vực 3D SHOWROOM phía trên. -->

    <!-- ===================================================== -->
    <!-- LOGIN MODAL -->
    <!-- ===================================================== -->

    <Teleport to="body">
      <div
        class="modal fade"
        id="loginModal"
        tabindex="-1"
        aria-labelledby="loginModalTitle"
        aria-hidden="true"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content modern-login-modal">
            <div class="login-modal-top">
              <div class="login-modal-icon">
                <i class="fa-solid fa-lock"></i>
              </div>

              <button class="modern-close" data-bs-dismiss="modal">
                <i class="fa-solid fa-xmark"></i>
              </button>
            </div>

            <div class="modal-body text-center">
              <span class="modal-kicker"> ACCOUNT REQUIRED </span>

              <h3 id="loginModalTitle">Bạn cần đăng nhập</h3>

              <p>
                Vui lòng đăng nhập để thêm sản phẩm vào giỏ hàng và tiếp tục mua
                sắm.
              </p>
            </div>

            <div class="modal-footer modern-modal-footer">
              <button class="modal-btn-secondary" data-bs-dismiss="modal">
                Đóng
              </button>

              <button class="modal-btn-primary" @click="goLogin">
                ĐI ĐẾN ĐĂNG NHẬP
                <i class="fa-solid fa-arrow-right"></i>
              </button>
            </div>
          </div>
        </div>
      </div>
    </Teleport>

    <!-- CHATBOT KHÁCH HÀNG: tách khỏi vùng overflow của trang để không bị cắt khung -->
    <Teleport to="body">
      <button
        type="button"
        class="chat-float"
        :class="{ 'chat-float-open': showChat }"
        :aria-label="showChat ? 'Đóng TechShop AI' : 'Mở TechShop AI'"
        :aria-expanded="showChat ? 'true' : 'false'"
        @click="toggleChat"
      >
        <span class="chat-pulse" v-if="!showChat"></span>
        <i :class="showChat ? 'fa-solid fa-xmark' : 'fa-solid fa-headset'"></i>
      </button>

      <section
        v-if="showChat"
        class="chat-wrapper"
        :class="{ 'chat-expanded': isExpanded }"
        role="dialog"
        aria-label="Trò chuyện với TechShop AI"
        aria-modal="false"
      >
        <header class="chat-top">
          <div class="chat-profile">
            <div class="avatar">
              <span class="avatar-ring"></span>
              <i class="fa-solid fa-robot"></i>
            </div>
            <div class="chat-profile-copy">
              <div class="chat-name-row">
                <h4>TechShop AI</h4>
                <span class="online-dot" aria-label="Trực tuyến"></span>
              </div>
              <p>Tư vấn máy tính và phụ kiện</p>
              <span class="chat-powered"
                ><i class="fa-solid fa-sparkles"></i> Trợ lý mua sắm thông
                minh</span
              >
            </div>
          </div>

          <div class="chat-actions">
  <button
    type="button"
    class="expand-btn"
    :aria-label="isExpanded ? 'Thu nhỏ khung chat' : 'Mở rộng khung chat'"
    :title="isExpanded ? 'Thu nhỏ' : 'Mở rộng'"
    @click="toggleExpand"
  >
    <i :class="isExpanded ? 'fa-solid fa-compress' : 'fa-solid fa-expand'"></i>
  </button>

  <button
    type="button"
    class="close-btn"
    aria-label="Đóng khung chat"
    @click="toggleChat"
  >
    <i class="fa-solid fa-xmark"></i>
  </button>
</div>
        </header>

        <div
          ref="chatContent"
          class="chat-content"
          role="log"
          aria-live="polite"
          aria-relevant="additions text"
        >
          <div class="chat-intro">
            <span class="chat-intro-icon"
              ><i class="fa-solid fa-wand-magic-sparkles"></i
            ></span>
            <div>
              <strong>Chào bạn! Mình là TechShop AI 👋</strong>
              <p>
                Cho mình biết nhu cầu hoặc ngân sách để tư vấn sản phẩm phù hợp
                nhé.
              </p>
            </div>
          </div>

          <div
            v-for="(msg, index) in messages"
            :key="index"
            :class="msg.type === 'user' ? 'user-chat' : 'bot-chat'"
          >
            <div
              v-if="msg.type === 'bot'"
              class="chat-avatar-small"
              aria-hidden="true"
            >
              <i class="fa-solid fa-robot"></i>
            </div>
            <!-- Render text safely; preserve the line breaks returned by Laravel/Ollama. -->
            <div class="chat-bubble" :class="msg.type">{{ msg.text }}</div>
          </div>

          <div
            v-if="isTyping"
            class="bot-chat typing-row"
            aria-label="TechShop AI đang trả lời"
          >
            <div class="chat-avatar-small" aria-hidden="true">
              <i class="fa-solid fa-robot"></i>
            </div>
            <div class="chat-bubble bot typing-bubble">
              <span></span><span></span><span></span>
              <small>AI đang suy nghĩ...</small>
            </div>
          </div>

          <div
            v-if="messages.length === 0 && !isTyping"
            class="chat-suggestions"
          >
            <span>Thử hỏi nhanh</span>
            <button
              type="button"
              @click="sendSuggestion('Tư vấn laptop theo ngân sách của tôi')"
            >
              <i class="fa-solid fa-laptop"></i> Tư vấn laptop
            </button>
            <button type="button" @click="sendSuggestion('Gợi ý PC gaming')">
              <i class="fa-solid fa-desktop"></i> PC gaming
            </button>
            <button
              type="button"
              @click="sendSuggestion('Tôi muốn tìm phụ kiện máy tính')"
            >
              <i class="fa-solid fa-keyboard"></i> Phụ kiện
            </button>
          </div>
        </div>

        <form class="chat-bottom" @submit.prevent="sendMessage">
          <div class="chat-input-wrap" :class="{ listening: isListening }">
  <div class="chat-input-field">
    <input
      ref="chatInput"
      type="text"
      v-model="inputMessage"
      :disabled="isTyping"
      autocomplete="off"
      maxlength="2000"
      aria-label="Nội dung cần hỏi TechShop AI"
      :placeholder="isListening ? 'Đang nghe bạn nói...' : 'Nhập câu hỏi của bạn...'"
    />

    <button
      v-if="speechSupported"
      type="button"
      class="mic-btn"
      :class="{ 'mic-active': isListening }"
      :disabled="isTyping"
      :aria-label="isListening ? 'Dừng ghi âm' : 'Nói để nhập'"
      :title="isListening ? 'Dừng ghi âm' : 'Nói để nhập'"
      @click="toggleMic"
    >
      <i :class="isListening ? 'fa-solid fa-stop' : 'fa-solid fa-microphone'"></i>
    </button>
  </div>

  <span class="chat-input-hint">
    {{ isListening ? "🎙️ Đang nghe... bấm nút đỏ để dừng" : "Enter để gửi" }}
  </span>
</div>
          <button
            type="submit"
            class="send-btn"
            :disabled="isTyping || !inputMessage.trim()"
            aria-label="Gửi tin nhắn"
          >
            <i
              :class="
                isTyping ? 'fa-solid fa-ellipsis' : 'fa-solid fa-arrow-up'
              "
            ></i>
          </button>
        </form>
        <div class="chat-footer-note">
          <i class="fa-solid fa-shield-halved"></i> Câu trả lời dựa trên dữ liệu
          sản phẩm của cửa hàng
        </div>
      </section>
    </Teleport>
  </div>
</template>




<script>
import * as bootstrap from "bootstrap";
import axios from "axios";
import { useToast } from "vue-toastification";

export default {
  data() {
    return {
      // =========================
      // PC FAKE - GIỮ NGUYÊN TẠM
      // =========================
      pcProducts: [
        {
          ten: "PC Gaming RTX 4060",
          gia: 24990000,
          ton: 12,
          danhgia: 4.9,
          chitiet: "Intel i5 13400F | RTX 4060",
          hinh: "https://th.bing.com/th/id/OIP.a9gYj8FJb-Cemchkya1UkQHaFj?w=246&h=185&c=7&r=0&o=7&dpr=1.5&pid=1.7&rm=3",
          soluong: 1,
        },
        {
          ten: "PC Gaming RTX 4070",
          gia: 32990000,
          ton: 5,
          danhgia: 5.0,
          chitiet: "Ryzen 7 | RTX 4070",
          hinh: "https://phuongthanhcomputer.com/wp-content/uploads/2025/02/pc-rtx-4070-i7-12700k-1-1.jpg",
          soluong: 1,
        },
        {
          ten: "PC Streaming",
          gia: 28990000,
          ton: 8,
          danhgia: 4.8,
          chitiet: "RTX 4060Ti | 32GB RAM",
          hinh: "https://storage-asset.msi.com/global/picture/article/seo_168231693464461e86d74365.66209087.jpeg",
          soluong: 1,
        },
        {
          ten: "PC Gaming White",
          gia: 35990000,
          ton: 3,
          danhgia: 5.0,
          chitiet: "Full White RTX 4070",
          hinh: "https://vitinhlongphat.com.vn/thumbs/800x734x2/upload/product/z571681954956517ed310d67ca7ac970037855f1664d20-4285.jpg",
          soluong: 1,
        },
      ],

      // =========================
      // SẢN PHẨM TỪ API
      // =========================
      products: [],

      // Danh mục lấy từ Backend
      categories: [],

      // Mặc định hiển thị tất cả sản phẩm
      selectedCategory: "all",

      showAllProducts: false,
      // =========================
      // CHAT AI
      // =========================
      showChat: false,
      inputMessage: "",
      isTyping: false,
      messages: [],

      // UUID sẽ được lấy từ API theo tài khoản hiện tại.
      conversationUuid: "",

            // Mở rộng khung chat
            isExpanded: false,

      // Ghi âm
      isListening: false,
      speechSupported: false,
      recognition: null,
      voiceBase: "",
    };
  },

  mounted() {
    this.getDataSanPham();
    this.getDataDanhMuc();
    this.initSpeech();
  },

  beforeUnmount() {
    try {
      this.recognition?.abort();
    } catch (e) {}
  },

  computed: {
    // Lọc sản phẩm theo danh mục đang chọn
    filteredProducts() {
        if (this.selectedCategory === "all") {
            return this.products;
        }

        return this.products.filter(
            (sp) =>
                Number(sp.MaDM) === Number(this.selectedCategory)
        );
    },

    // Hiển thị sản phẩm
    displayProducts() {
        const sortedProducts = [...this.filteredProducts].sort(
            (a, b) => Number(b.GiaBan || 0) - Number(a.GiaBan || 0)
        );

        return this.showAllProducts
            ? sortedProducts
            : sortedProducts.slice(0, 4);
    },
},

  methods: {

    // LẤY DANH MỤC TỪ BACKEND
async getDataDanhMuc() {
    try {
        const res = await axios.get(
            "http://127.0.0.1:8000/api/client/danh-muc/get-data",
            {
                timeout: 12000
            }
        );

        if (res.data?.status === true) {
            this.categories = (res.data.data || []).sort(
                (a, b) => Number(a.MaDM) - Number(b.MaDM)
            );
        }
    } catch (error) {
        console.error(
            "Lỗi tải danh mục:",
            error.response?.data || error
        );
    }
},

// CHUYỂN MÃ THÀNH DẠNG DM001, DM002...
maDanhMucHienThi(maDM) {
    return "DM" + String(maDM).padStart(3, "0");
},

// CHỌN ICON THEO TÊN DANH MỤC
iconDanhMuc(tenDM) {
    const ten = String(tenDM || "").toLowerCase();

    if (ten.includes("laptop")) {
        return "fa-solid fa-laptop";
    }

    if (ten.includes("pc gaming")) {
        return "fa-solid fa-desktop";
    }

    if (ten.includes("màn hình") || ten.includes("man hinh")) {
        return "fa-solid fa-display";
    }

    if (ten.includes("bàn phím") || ten.includes("ban phim")) {
        return "fa-regular fa-keyboard";
    }

    if (ten.includes("chuột") || ten.includes("chuot")) {
        return "fa-solid fa-computer-mouse";
    }

    if (ten.includes("tai nghe")) {
        return "fa-solid fa-headphones";
    }

    if (ten.includes("linh kiện") || ten.includes("linh kien")) {
        return "fa-solid fa-microchip";
    }

    if (ten.includes("phụ kiện") || ten.includes("phu kien")) {
        return "fa-solid fa-puzzle-piece";
    }

    return "fa-solid fa-layer-group";
},

// BẤM DANH MỤC ĐỂ LỌC SẢN PHẨM
chonDanhMuc(maDM) {
    this.selectedCategory = maDM;

    // Khi đổi danh mục thì thu gọn sản phẩm
    this.showAllProducts = false;

    this.$nextTick(() => {
        document
            .querySelector(".product-section-wrap")
            ?.scrollIntoView({
                behavior: "smooth",
                block: "start"
            });
    });
},
    // =========================
    // HIỂN THỊ SẢN PHẨM
    // =========================
    toggleShowAllProducts() {
      this.showAllProducts = !this.showAllProducts;

      if (!this.showAllProducts) {
        this.$nextTick(() => {
          document.querySelector(".product-section-wrap")?.scrollIntoView({
            behavior: "smooth",
            block: "start",
          });
        });
      }
    },

    tiltProductCard(event) {
      if (window.matchMedia("(prefers-reduced-motion: reduce)").matches) {
        return;
      }

      const card = event.currentTarget;
      const rect = card.getBoundingClientRect();
      const x = (event.clientX - rect.left) / rect.width;
      const y = (event.clientY - rect.top) / rect.height;
      const rotateY = (x - 0.5) * 7;
      const rotateX = (0.5 - y) * 7;

      card.style.transform = `perspective(1100px) rotateX(${rotateX}deg) rotateY(${rotateY}deg) translateY(-7px)`;

      card.style.setProperty("--spot-x", `${x * 100}%`);
      card.style.setProperty("--spot-y", `${y * 100}%`);
    },

    resetProductCard(event) {
      const card = event.currentTarget;
      card.style.transform = "";
      card.style.removeProperty("--spot-x");
      card.style.removeProperty("--spot-y");
    },

    // =========================
    // LẤY SẢN PHẨM TỪ API
    // =========================
    getDataSanPham() {
      axios
        .get("http://127.0.0.1:8000/api/san-pham/get-data")
        .then((res) => {
          if (res.data.status) {
            this.products = res.data.data;
          }
        })
        .catch((error) => {
          console.log("Lỗi lấy sản phẩm:", error);
        });
    },

    // =========================
    // CLICK SẢN PHẨM
    // =========================
    clickProduct(item) {
      if (item.TrangThaiSP === "NgungBan") {
        alert(
          "Sản phẩm đã ngừng kinh doanh. Xin lỗi quý khách, sản phẩm này hiện không thể đặt mua."
        );
        return;
      }

      const checkLogin = localStorage.getItem("isLogin");

      if (checkLogin === "true") {
        this.$router.push({
          path: "/chi-tiet-sp",
          query: {
            MaSP: item.MaSP,
          },
        });
      } else {
        const modalElement = document.getElementById("loginModal");

        if (modalElement) {
          bootstrap.Modal.getOrCreateInstance(modalElement).show();
        }
      }
    },

    // =========================
    // GIỎ HÀNG
    // =========================

    // =========================
    // THÊM SẢN PHẨM VÀO GIỎ HÀNG DATABASE
    // =========================
    async themGioHang(item) {
      const toast = useToast();

      // Không cho thêm sản phẩm đã ngừng kinh doanh
      if (item.TrangThaiSP === "NgungBan") {
        toast.error("Sản phẩm đã ngừng kinh doanh.");
        return;
      }

      // Không cho thêm sản phẩm hết hàng
      if (Number(item.SoLuongTon) <= 0) {
        toast.error("Sản phẩm hiện đã hết hàng.");
        return;
      }

      const token = localStorage.getItem("token");
      const checkLogin = localStorage.getItem("isLogin");

      // Chưa đăng nhập thì yêu cầu đăng nhập
      if (!token || checkLogin !== "true") {
        const modalElement = document.getElementById("loginModal");

        if (modalElement) {
          bootstrap.Modal.getOrCreateInstance(modalElement).show();
        }

        return;
      }

      try {
        // Gọi backend để lưu sản phẩm vào giỏ của tài khoản
        const response = await axios.post(
          "http://127.0.0.1:8000/api/gio-hang/add-data",
          {
            MaSP: item.MaSP,
            SoLuong: 1,
          },
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
            timeout: 15000,
          }
        );

        if (response.data.status === true) {
          // Báo cho Header tải lại giỏ từ database
          window.dispatchEvent(new Event("storage"));

          toast.success("Đã thêm sản phẩm vào giỏ hàng 🛒");
        } else {
          toast.error(
            response.data.message || "Không thể thêm sản phẩm vào giỏ hàng."
          );
        }
      } catch (error) {
        console.error("Lỗi thêm giỏ hàng:", error);

        toast.error(
          error.response?.data?.message ||
            error.response?.data?.errors?.SoLuong?.[0] ||
            "Không thể thêm sản phẩm. Bạn kiểm tra đăng nhập và kết nối máy chủ nhé."
        );
      }
    },

    // =========================
    // ĐI ĐĂNG NHẬP
    // =========================
    goLogin() {
      const modalElement = document.getElementById("loginModal");

      const chuyenTrangDangNhap = () => {
        document
          .querySelectorAll(".modal-backdrop")
          .forEach((el) => el.remove());

        document.body.classList.remove("modal-open");
        document.body.style.removeProperty("overflow");
        document.body.style.removeProperty("padding-right");

        this.$router.push("/dang-nhap");
      };

      const modalInstance = modalElement
        ? bootstrap.Modal.getInstance(modalElement)
        : null;

      if (modalInstance && modalElement.classList.contains("show")) {
        modalElement.addEventListener("hidden.bs.modal", chuyenTrangDangNhap, {
          once: true,
        });

        modalInstance.hide();
      } else {
        chuyenTrangDangNhap();
      }
    },

    // =========================
    // MỞ / ĐÓNG CHAT
    // =========================
    toggleChat() {
      this.showChat = !this.showChat;

      if (this.showChat) {
        this.loadChatHistory();
      } else if (this.isListening) {
        this.recognition?.stop();
      }
    },

    // Phóng to / thu nhỏ
    toggleExpand() {
      this.isExpanded = !this.isExpanded;
      this.$nextTick(() => this.scrollChatToBottom());
    },

    // Khởi tạo nhận diện giọng nói
    initSpeech() {
      const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
      if (!SR) return;

      this.speechSupported = true;

      const rec = new SR();
      rec.lang = "vi-VN";
      rec.interimResults = true;
      rec.continuous = false;

      rec.onstart = () => {
        this.isListening = true;
      };

      rec.onresult = (event) => {
        let text = "";
        for (let i = 0; i < event.results.length; i++) {
          text += event.results[i][0].transcript;
        }
        this.inputMessage = (this.voiceBase + " " + text).trim();
      };

      rec.onerror = (event) => {
        this.isListening = false;
        if (event.error === "not-allowed" || event.error === "service-not-allowed") {
          alert("Bạn cần cho phép trình duyệt dùng micro để ghi âm nhé.");
        }
      };

      rec.onend = () => {
        this.isListening = false;
        this.$nextTick(() => this.$refs.chatInput?.focus());
      };

      this.recognition = rec;
    },

    // Bật / tắt ghi âm
    toggleMic() {
      if (!this.recognition || this.isTyping) return;

      if (this.isListening) {
        this.recognition.stop();
        return;
      }

      // Giữ lại chữ đã gõ, giọng nói sẽ nối thêm vào sau
      this.voiceBase = this.inputMessage.trim();

      try {
        this.recognition.start();
      } catch (e) {}
    },

    // =========================
    // TẢI LỊCH SỬ CHAT TỪ DATABASE
    // =========================
    async loadChatHistory() {
      // Xóa tin nhắn đang hiển thị để tránh lẫn tài khoản
      this.messages = [];

      try {
        const token = localStorage.getItem("token");

        const response = await axios.get(
          "http://127.0.0.1:8000/api/chatbot/history",
          {
            timeout: 15000,
            headers: token ? { Authorization: `Bearer ${token}` } : {},
          }
        );

        if (response?.data?.status === true) {
          const history = response.data.data;

          this.messages = Array.isArray(history)
            ? history.map((item) => ({
                type: item.type,
                text: item.text,
              }))
            : [];

          this.conversationUuid = response.data.conversation_uuid || "";

          this.$nextTick(() => this.scrollChatToBottom());
        }
      } catch (error) {
        console.error("Lỗi tải lịch sử chatbot:", error);

        this.messages.push({
          type: "bot",
          text: "Chưa tải được lịch sử chat. Bạn thử đóng rồi mở lại khung chat nhé.",
        });
      }
    },

    scrollChatToBottom() {
      const panel = this.$refs.chatContent;

      if (panel) {
        panel.scrollTop = panel.scrollHeight;
      }
    },

    sendSuggestion(message) {
      if (this.isTyping) return;

      this.inputMessage = message;
      this.sendMessage();
    },

    // =========================
    // CHAT AI
    // Khách vãng lai dùng chung hội thoại.
    // Tài khoản đăng nhập dùng hội thoại riêng.
    // =========================
    async sendMessage() {
      const message = this.inputMessage.trim();

      if (!message || this.isTyping) return;
      if (this.isListening) this.recognition?.stop();

      this.messages.push({
        type: "user",
        text: message,
      });

      this.inputMessage = "";
      this.isTyping = true;

      this.$nextTick(() => this.scrollChatToBottom());

      try {
        const token = localStorage.getItem("token");

        const response = await axios.post(
          "http://127.0.0.1:8000/api/chatbot",
          {
            message: message,

            // Backend tự chọn hội thoại theo tài khoản.
            // Không gửi UUID cũ để tránh dùng nhầm hội thoại.
            conversation_uuid: null,
          },
          {
            timeout: 125000,
            headers: token ? { Authorization: `Bearer ${token}` } : {},
          }
        );

        const answer = response?.data?.data;

        if (
          response?.data?.status !== true ||
          typeof answer !== "string" ||
          !answer.trim()
        ) {
          throw new Error("EMPTY_AI_RESPONSE");
        }

        // Nhận UUID từ backend để tiện theo dõi,
        // nhưng backend vẫn là nơi quyết định hội thoại.
        this.conversationUuid = response.data.conversation_uuid || "";

        this.messages.push({
          type: "bot",
          text: answer.trim(),
        });
      } catch (error) {
        console.error("Lỗi gọi TechShop AI:", error);

        let errorMessage =
          "Mình chưa lấy được câu trả lời lúc này. Bạn thử gửi lại sau ít giây nhé.";

        if (error?.code === "ECONNABORTED") {
          errorMessage =
            "Câu hỏi đang xử lý hơi lâu. Bạn thử hỏi ngắn gọn hơn nhé.";
        } else if (error?.message === "EMPTY_AI_RESPONSE") {
          errorMessage =
            "AI chưa tạo được câu trả lời. Bạn thử diễn đạt lại nhé.";
        } else if (!error?.response) {
          errorMessage =
            "Không kết nối được máy chủ chatbot. Bạn kiểm tra Laravel và Ollama nhé.";
        } else if (error?.response?.status >= 500) {
          errorMessage = "Máy chủ chatbot đang gặp sự cố. Bạn thử lại sau nhé.";
        }

        this.messages.push({
          type: "bot",
          text: errorMessage,
        });
      } finally {
        this.isTyping = false;

        this.$nextTick(() => this.scrollChatToBottom());
      }
    },
  },
};
</script>

<style>
/* =========================================================
   HOME GLOBAL
========================================================= */

.home-page {
  position: relative;
  min-height: 100vh;

  padding-bottom: 70px;

  color: #111827;

  background: radial-gradient(
      circle at 10% 10%,
      rgba(239, 68, 68, 0.05),
      transparent 28%
    ),
    radial-gradient(
      circle at 90% 30%,
      rgba(59, 130, 246, 0.04),
      transparent 28%
    ),
    #f7f8fb;

  overflow: hidden;
}

.home-page::before {
  content: "";

  position: fixed;

  width: 500px;
  height: 500px;

  top: 20%;
  left: -250px;

  border-radius: 50%;

  background: radial-gradient(
    circle,
    rgba(239, 68, 68, 0.055),
    transparent 65%
  );

  pointer-events: none;
  z-index: 0;
}

.home-page > section {
  position: relative;
  z-index: 1;
}

/* =========================================================
   HERO
========================================================= */

.hero-zone {
  padding-top: 22px;
}

.hero-layout {
  display: grid;

  grid-template-columns:
    minmax(0, 1fr)
    270px;

  gap: 18px;
}

.hero-main {
  min-width: 0;
}

.hero-image-wrap {
  position: relative;

  min-height: 560px;

  border-radius: 32px;

  overflow: hidden;

  background: #111;

  box-shadow: 0 25px 70px rgba(15, 23, 42, 0.15),
    0 5px 15px rgba(15, 23, 42, 0.05);

  isolation: isolate;
}

.hero-main-image {
  width: 100%;
  height: 100%;

  min-height: 560px;

  object-fit: cover;

  display: block;

  transition: transform 1s cubic-bezier(0.2, 0.8, 0.2, 1);
}

.hero-image-wrap:hover .hero-main-image {
  transform: scale(1.035);
}

.hero-shade {
  position: absolute;
  inset: 0;

  background: linear-gradient(
    90deg,
    rgba(2, 6, 23, 0.86) 0%,
    rgba(2, 6, 23, 0.55) 35%,
    rgba(2, 6, 23, 0.12) 72%,
    rgba(2, 6, 23, 0.25) 100%
  );

  z-index: 1;
}

.hero-content {
  position: absolute;

  left: 55px;
  top: 50%;

  transform: translateY(-50%);

  z-index: 3;

  max-width: 520px;
}

.hero-badge {
  display: inline-flex;
  align-items: center;
  gap: 9px;

  padding: 8px 13px;

  margin-bottom: 20px;

  border: 1px solid rgba(255, 255, 255, 0.18);

  background: rgba(255, 255, 255, 0.1);

  backdrop-filter: blur(12px);

  border-radius: 999px;

  color: #fff;

  font-size: 11px;
  font-weight: 800;

  letter-spacing: 1.5px;
}

.pulse-dot {
  width: 7px;
  height: 7px;

  border-radius: 50%;

  background: #ff4d67;

  box-shadow: 0 0 0 5px rgba(255, 77, 103, 0.14),
    0 0 20px rgba(255, 77, 103, 0.75);

  animation: dotPulse 1.8s infinite;
}

@keyframes dotPulse {
  0%,
  100% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.35);
    opacity: 0.6;
  }
}

.hero-content h1 {
  margin: 0;

  color: white;

  font-size: clamp(48px, 5vw, 86px);

  line-height: 0.94;

  font-weight: 900;

  letter-spacing: -4px;
}

.hero-content h1 span {
  display: block;

  color: transparent;

  background: linear-gradient(90deg, #ff375f, #ff7654);

  -webkit-background-clip: text;
  background-clip: text;
}

.hero-content p {
  margin: 22px 0 26px;

  color: rgba(255, 255, 255, 0.75);

  font-size: 16px;

  letter-spacing: 0.6px;
}

.hero-actions {
  display: flex;
  align-items: center;

  gap: 20px;
}

.hero-btn {
  border: none;

  display: inline-flex;
  align-items: center;
  gap: 14px;

  padding: 14px 20px;

  border-radius: 14px;

  color: white;

  font-size: 13px;
  font-weight: 800;

  letter-spacing: 0.5px;

  background: linear-gradient(135deg, #ef4444, #ff5a57);

  box-shadow: 0 14px 30px rgba(239, 68, 68, 0.3);

  cursor: pointer;

  transition: 0.3s ease;
}

.hero-btn:hover {
  transform: translateY(-4px) scale(1.02);

  box-shadow: 0 18px 38px rgba(239, 68, 68, 0.38);
}

.hero-btn i {
  transition: 0.3s;
}

.hero-btn:hover i {
  transform: translateX(5px);
}

.hero-mini-stat {
  display: flex;
  flex-direction: column;
}

.hero-mini-stat strong {
  color: #fff;

  font-size: 20px;

  line-height: 1;
}

.hero-mini-stat span {
  margin-top: 5px;

  color: rgba(255, 255, 255, 0.55);

  font-size: 9px;

  font-weight: 700;

  letter-spacing: 2px;
}

.hero-floating-chip {
  position: absolute;

  z-index: 4;

  display: flex;
  align-items: center;
  gap: 8px;

  padding: 10px 14px;

  border-radius: 999px;

  color: #fff;

  font-size: 10px;
  font-weight: 800;

  letter-spacing: 1px;

  background: rgba(255, 255, 255, 0.09);

  border: 1px solid rgba(255, 255, 255, 0.12);

  backdrop-filter: blur(14px);

  box-shadow: 0 16px 35px rgba(0, 0, 0, 0.18);

  animation: floatingChip 5s ease-in-out infinite;
}

.hero-floating-chip i {
  color: #ff526a;
}

.chip-one {
  top: 32px;
  right: 35px;
}

.chip-two {
  right: 35px;
  bottom: 34px;

  animation-delay: -2.5s;
}

@keyframes floatingChip {
  0%,
  100% {
    transform: translateY(0);
  }

  50% {
    transform: translateY(-8px);
  }
}

/* =========================================================
   HERO SIDE
========================================================= */

.hero-side {
  display: grid;

  grid-template-rows: repeat(3, 1fr);

  gap: 18px;
}

.hero-promo-card {
  position: relative;

  overflow: hidden;

  border-radius: 22px;

  background: #fff;

  border: 1px solid rgba(15, 23, 42, 0.06);

  box-shadow: 0 15px 40px rgba(15, 23, 42, 0.08);

  cursor: pointer;
}

.hero-promo-card img {
  width: 100%;
  height: 100%;

  object-fit: cover;

  display: block;

  transition: transform 0.7s cubic-bezier(0.2, 0.8, 0.2, 1);
}

.hero-promo-card:hover img {
  transform: scale(1.08);
}

.promo-shine {
  position: absolute;

  width: 120px;
  height: 200%;

  top: -50%;

  left: -120%;

  transform: rotate(22deg);

  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.35),
    transparent
  );

  transition: 0.9s;
}

.hero-promo-card:hover .promo-shine {
  left: 150%;
}

/* =========================================================
   SERVICES
========================================================= */

.service-section {
  margin-top: 18px;
}

.service-strip {
  display: grid;

  grid-template-columns: repeat(4, 1fr);

  gap: 14px;
}

.service-card {
  display: flex;

  align-items: center;

  gap: 14px;

  padding: 18px;

  border-radius: 18px;

  background: rgba(255, 255, 255, 0.82);

  border: 1px solid rgba(226, 232, 240, 0.95);

  box-shadow: 0 8px 28px rgba(15, 23, 42, 0.045);

  backdrop-filter: blur(12px);

  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.service-card:hover {
  transform: translateY(-5px);

  box-shadow: 0 18px 35px rgba(15, 23, 42, 0.08);
}

.service-icon {
  width: 46px;
  height: 46px;

  flex-shrink: 0;

  display: flex;
  align-items: center;
  justify-content: center;

  border-radius: 14px;

  color: #ef4444;

  background: linear-gradient(135deg, #fff1f2, #ffe4e6);

  font-size: 18px;
}

.service-card strong {
  display: block;

  color: #111827;

  font-size: 13px;

  font-weight: 800;
}

.service-card span {
  display: block;

  margin-top: 4px;

  color: #9ca3af;

  font-size: 11px;
}

/* =========================================================
   CATEGORY
========================================================= */

.category-section {
  margin-top: 55px;
}

.category-layout {
  display: grid;

  grid-template-columns: 290px minmax(0, 1fr);

  gap: 18px;
}

.category-panel {
  border-radius: 24px;

  overflow: hidden;

  background: linear-gradient(180deg, #111827, #0f172a);

  box-shadow: 0 22px 55px rgba(15, 23, 42, 0.15);
}

.category-header {
  display: flex;

  align-items: center;

  gap: 12px;

  padding: 22px;

  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
}

.category-header-icon {
  width: 44px;
  height: 44px;

  display: flex;
  align-items: center;
  justify-content: center;

  border-radius: 14px;

  color: white;

  background: linear-gradient(135deg, #ef4444, #ff5b5b);

  box-shadow: 0 10px 25px rgba(239, 68, 68, 0.3);
}

.category-header span {
  color: #94a3b8;

  font-size: 9px;
  font-weight: 800;

  letter-spacing: 2px;
}

.category-header h3 {
  margin: 3px 0 0;

  color: white;

  font-size: 18px;

  font-weight: 900;
}

.category-list {
  padding: 8px;
}

.category-item {
  display: flex;

  justify-content: space-between;
  align-items: center;

  padding: 14px 13px;

  color: #cbd5e1;

  border-radius: 14px;

  cursor: pointer;

  transition: 0.25s ease;
}

.category-left {
  display: flex;
  align-items: center;

  gap: 12px;
}

.category-left i {
  width: 20px;

  text-align: center;

  color: #94a3b8;

  transition: 0.25s;
}

.category-item > i {
  color: #64748b;

  font-size: 10px;

  transition: 0.25s;
}

.category-item:hover,
.category-item.active {
  color: white;

  background: linear-gradient(
    90deg,
    rgba(239, 68, 68, 0.18),
    rgba(239, 68, 68, 0.05)
  );

  transform: translateX(3px);
}

.category-item:hover .category-left i,
.category-item.active .category-left i {
  color: #ff596d;
}

.category-item:hover > i,
.category-item.active > i {
  color: #ff596d;

  transform: translateX(4px);
}

/* =========================================================
   PROMO AREA
========================================================= */

.promo-area {
  display: grid;

  grid-template-rows: 1.5fr 0.6fr;

  gap: 18px;
}

.promo-big {
  position: relative;

  min-height: 260px;

  overflow: hidden;

  border-radius: 24px;

  background: #111827;

  box-shadow: 0 18px 50px rgba(15, 23, 42, 0.09);
}

.promo-big img {
  width: 100%;
  height: 100%;

  object-fit: cover;

  display: block;

  transition: 1s;
}

.promo-big:hover img {
  transform: scale(1.03);
}

.promo-overlay {
  position: absolute;

  inset: 0;

  padding: 30px;

  display: flex;
  flex-direction: column;
  justify-content: center;

  background: linear-gradient(
    90deg,
    rgba(15, 23, 42, 0.78),
    rgba(15, 23, 42, 0.18)
  );
}

.promo-tag {
  width: max-content;

  padding: 7px 11px;

  border-radius: 999px;

  color: #fff;

  background: rgba(239, 68, 68, 0.8);

  font-size: 9px;
  font-weight: 900;

  letter-spacing: 1.5px;
}

.promo-overlay h2 {
  margin: 14px 0 6px;

  color: white;

  font-size: 36px;

  line-height: 1;

  font-weight: 900;
}

.promo-overlay h2 span {
  display: block;

  color: #ff596d;
}

.promo-overlay p {
  margin: 0;

  color: rgba(255, 255, 255, 0.7);

  font-size: 13px;
}

.promo-small-row {
  display: grid;

  grid-template-columns: repeat(3, 1fr);

  gap: 12px;
}

.promo-small {
  overflow: hidden;

  min-height: 120px;

  border-radius: 18px;

  background: #fff;

  box-shadow: 0 12px 32px rgba(15, 23, 42, 0.07);
}

.promo-small img {
  width: 100%;
  height: 100%;

  display: block;

  object-fit: cover;

  transition: 0.6s;
}

.promo-small:hover img {
  transform: scale(1.07);
}

/* =========================================================
   3D SHOWROOM
========================================================= */

.showroom-section {
  margin-top: 70px;
}

.showroom {
  position: relative;

  min-height: 640px;

  padding: 60px;

  display: grid;

  grid-template-columns: 40% 60%;

  overflow: hidden;

  border-radius: 36px;

  background: radial-gradient(
      circle at 70% 50%,
      rgba(59, 130, 246, 0.13),
      transparent 26%
    ),
    radial-gradient(circle at 15% 10%, rgba(239, 68, 68, 0.18), transparent 28%),
    linear-gradient(135deg, #070b13, #0c1220 55%, #101827);

  box-shadow: 0 30px 80px rgba(2, 6, 23, 0.2);

  perspective: 1600px;

  isolation: isolate;
}

.showroom::before {
  content: "";

  position: absolute;
  inset: 0;

  border-radius: 36px;

  border: 1px solid rgba(255, 255, 255, 0.08);

  pointer-events: none;

  z-index: 5;
}

.showroom-grid {
  position: absolute;
  inset: 0;

  opacity: 0.15;

  background-image: linear-gradient(
      rgba(255, 255, 255, 0.06) 1px,
      transparent 1px
    ),
    linear-gradient(90deg, rgba(255, 255, 255, 0.06) 1px, transparent 1px);

  background-size: 45px 45px;

  transform: perspective(700px) rotateX(65deg) translateY(180px) scale(1.8);

  transform-origin: center bottom;

  z-index: 0;
}

.showroom-glow {
  position: absolute;

  border-radius: 50%;

  filter: blur(70px);

  pointer-events: none;
}

.glow-left {
  width: 300px;
  height: 300px;

  left: -100px;
  top: 40px;

  background: rgba(239, 68, 68, 0.11);
}

.glow-right {
  width: 330px;
  height: 330px;

  right: 80px;
  bottom: -120px;

  background: rgba(59, 130, 246, 0.1);
}

.showroom-info {
  position: relative;

  z-index: 4;

  align-self: center;

  max-width: 430px;
}

.showroom-eyebrow {
  display: flex;

  align-items: center;

  gap: 10px;

  color: #94a3b8;

  font-size: 10px;

  font-weight: 800;

  letter-spacing: 2px;
}

.showroom-eyebrow span {
  width: 34px;
  height: 2px;

  border-radius: 20px;

  background: #ef4444;

  box-shadow: 0 0 18px rgba(239, 68, 68, 0.8);
}

.showroom-info h2 {
  margin: 18px 0;

  color: white;

  font-size: clamp(42px, 4vw, 66px);

  line-height: 0.95;

  font-weight: 900;

  letter-spacing: -3px;
}

.showroom-info h2 span {
  display: block;

  color: transparent;

  background: linear-gradient(90deg, #ff416c, #ff7c63);

  -webkit-background-clip: text;
  background-clip: text;
}

.showroom-info > p {
  max-width: 390px;

  color: #94a3b8;

  font-size: 14px;

  line-height: 1.8;
}

.showroom-tags {
  display: flex;

  flex-wrap: wrap;

  gap: 8px;

  margin-top: 22px;
}

.showroom-tags span {
  display: inline-flex;
  align-items: center;
  gap: 7px;

  padding: 8px 11px;

  border-radius: 999px;

  color: #cbd5e1;

  background: rgba(255, 255, 255, 0.05);

  border: 1px solid rgba(255, 255, 255, 0.08);

  font-size: 9px;
  font-weight: 800;

  letter-spacing: 0.8px;
}

.showroom-tags i {
  color: #ff586d;
}

.showroom-line {
  width: 80px;
  height: 2px;

  margin: 28px 0;

  background: linear-gradient(90deg, #ef4444, transparent);
}

.showroom-note {
  display: flex;

  align-items: center;

  gap: 12px;
}

.showroom-note-icon {
  width: 42px;
  height: 42px;

  display: flex;
  align-items: center;
  justify-content: center;

  border-radius: 13px;

  color: white;

  background: rgba(255, 255, 255, 0.06);

  border: 1px solid rgba(255, 255, 255, 0.08);

  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.06);
}

.showroom-note strong {
  display: block;

  color: #e2e8f0;

  font-size: 11px;

  letter-spacing: 1px;
}

.showroom-note span {
  display: block;

  margin-top: 3px;

  color: #64748b;

  font-size: 10px;
}

/* =========================================================
   3D SCENE
========================================================= */

.showroom-scene {
  position: relative;

  min-height: 520px;

  display: flex;

  align-items: center;
  justify-content: center;

  z-index: 3;

  perspective: 1800px;
}

.pc-orbit {
  position: relative;

  width: 410px;
  height: 410px;

  transform-style: preserve-3d;

  animation: orbitSpin 18s linear infinite;

  z-index: 3;
}

.showroom-scene:hover .pc-orbit {
  animation-play-state: paused;
}

@keyframes orbitSpin {
  from {
    transform: rotateX(-4deg) rotateY(0deg);
  }

  to {
    transform: rotateX(-4deg) rotateY(360deg);
  }
}

.pc-3d-card {
  position: absolute;

  left: 50%;
  top: 50%;

  width: 250px;
  height: 330px;

  margin-left: -125px;
  margin-top: -165px;

  transform-style: preserve-3d;

  transform: rotateY(calc(var(--index) * 90deg)) translateZ(245px);

  transition: filter 0.3s ease;
}

.pc-3d-card:hover {
  filter: brightness(1.08) drop-shadow(0 15px 25px rgba(239, 68, 68, 0.18));
}

.pc-3d-inner {
  width: 100%;
  height: 100%;

  padding: 15px;

  border-radius: 24px;

  background: linear-gradient(
    145deg,
    rgba(255, 255, 255, 0.12),
    rgba(255, 255, 255, 0.04)
  );

  border: 1px solid rgba(255, 255, 255, 0.12);

  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.08),
    0 25px 50px rgba(0, 0, 0, 0.25);

  backdrop-filter: blur(16px);

  transform-style: preserve-3d;
}

.pc-card-top {
  display: flex;

  justify-content: space-between;
  align-items: center;

  margin-bottom: 10px;
}

.pc-label {
  color: #fb7185;

  font-size: 8px;
  font-weight: 900;

  letter-spacing: 1.7px;
}

.pc-number {
  color: #64748b;

  font-size: 11px;

  font-weight: 900;
}

.pc-image-wrap {
  position: relative;

  height: 165px;

  display: flex;
  align-items: center;
  justify-content: center;

  overflow: hidden;

  border-radius: 17px;

  background: radial-gradient(
    circle,
    rgba(255, 255, 255, 0.06),
    transparent 70%
  );

  transform: translateZ(18px);
}

.pc-image-wrap img {
  width: 100%;
  height: 100%;

  object-fit: contain;

  position: relative;

  z-index: 2;

  filter: drop-shadow(0 15px 22px rgba(0, 0, 0, 0.3));

  animation: productFloat 4s ease-in-out infinite;
}

@keyframes productFloat {
  0%,
  100% {
    transform: translateY(0);
  }

  50% {
    transform: translateY(-6px);
  }
}

.image-glow {
  position: absolute;

  width: 130px;
  height: 80px;

  border-radius: 50%;

  background: radial-gradient(
    ellipse,
    rgba(239, 68, 68, 0.32),
    transparent 70%
  );

  filter: blur(25px);

  z-index: 1;
}

.pc-card-content {
  padding: 13px 3px 0;

  transform: translateZ(22px);
}

.pc-card-content h4 {
  margin: 0;

  color: white;

  font-size: 13px;

  font-weight: 800;

  white-space: nowrap;

  overflow: hidden;

  text-overflow: ellipsis;
}

.pc-card-content p {
  margin: 7px 0;

  color: #94a3b8;

  font-size: 10px;

  line-height: 1.5;

  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;

  overflow: hidden;
}

.pc-bottom {
  display: flex;

  justify-content: space-between;
  align-items: center;

  gap: 8px;
}

.pc-bottom strong {
  color: #ff5b70;

  font-size: 15px;
}

.pc-bottom span {
  color: #fbbf24;

  font-size: 10px;
  font-weight: 800;
}

/* =========================================================
   3D CORE
========================================================= */

.showroom-core {
  position: absolute;

  width: 120px;
  height: 120px;

  top: 50%;
  left: 50%;

  transform: translate(-50%, -50%);

  transform-style: preserve-3d;

  z-index: 1;
}

.core-center {
  position: absolute;

  inset: 24px;

  border-radius: 50%;

  display: flex;
  align-items: center;
  justify-content: center;

  flex-direction: column;

  color: white;

  background: radial-gradient(
    circle at 30% 30%,
    rgba(255, 255, 255, 0.2),
    rgba(255, 255, 255, 0.05)
  );

  border: 1px solid rgba(255, 255, 255, 0.14);

  box-shadow: 0 0 40px rgba(239, 68, 68, 0.16),
    inset 0 0 25px rgba(255, 255, 255, 0.05);

  backdrop-filter: blur(10px);
}

.core-center i {
  font-size: 23px;

  color: #ff526b;

  margin-bottom: 6px;
}

.core-center span {
  font-size: 9px;

  font-weight: 900;

  letter-spacing: 2px;
}

.core-ring {
  position: absolute;

  inset: 0;

  border-radius: 50%;

  border: 1px solid rgba(239, 68, 68, 0.3);

  border-top-color: transparent;
  border-bottom-color: transparent;
}

.ring-a {
  animation: coreSpin 5s linear infinite;
}

.ring-b {
  transform: scale(0.78) rotateX(65deg);

  border-color: rgba(96, 165, 250, 0.2);

  animation: coreSpinReverse 4s linear infinite;
}

@keyframes coreSpin {
  to {
    transform: rotateZ(360deg);
  }
}

@keyframes coreSpinReverse {
  to {
    transform: scale(0.78) rotateX(65deg) rotateZ(-360deg);
  }
}

.scene-platform {
  position: absolute;

  width: 420px;
  height: 95px;

  bottom: 82px;

  border-radius: 50%;

  background: radial-gradient(
    ellipse,
    rgba(239, 68, 68, 0.17),
    rgba(239, 68, 68, 0.03) 52%,
    transparent 72%
  );

  filter: blur(2px);

  transform: rotateX(72deg);

  animation: platformPulse 4s ease-in-out infinite;
}

@keyframes platformPulse {
  0%,
  100% {
    opacity: 0.7;
    transform: rotateX(72deg) scale(1);
  }

  50% {
    opacity: 1;
    transform: rotateX(72deg) scale(1.08);
  }
}

.scene-ring {
  position: absolute;

  width: 300px;
  height: 300px;

  border-radius: 50%;

  border: 1px solid rgba(255, 255, 255, 0.05);

  transform: rotateX(70deg);

  z-index: 0;
}

.ring-one {
  animation: sceneRingSpin 13s linear infinite;
}

.ring-two {
  width: 390px;
  height: 390px;

  animation: sceneRingSpinReverse 17s linear infinite;
}

@keyframes sceneRingSpin {
  to {
    transform: rotateX(70deg) rotateZ(360deg);
  }
}

@keyframes sceneRingSpinReverse {
  to {
    transform: rotateX(70deg) rotateZ(-360deg);
  }
}

.scene-shadow {
  position: absolute;

  bottom: 52px;

  width: 320px;
  height: 70px;

  border-radius: 50%;

  background: radial-gradient(ellipse, rgba(0, 0, 0, 0.35), transparent 70%);

  filter: blur(12px);

  z-index: 0;
}

/* =========================================================
   PRODUCT SECTION
========================================================= */

.product-section-wrap {
  margin-top: 75px;
}

.section-head-modern {
  display: flex;

  align-items: flex-end;

  justify-content: space-between;

  gap: 20px;

  margin-bottom: 24px;
}

.section-kicker {
  color: #ef4444;

  font-size: 9px;

  font-weight: 900;

  letter-spacing: 2px;
}

.section-head-modern h2 {
  margin: 7px 0 5px;

  color: #0f172a;

  font-size: 34px;

  line-height: 1;

  font-weight: 900;

  letter-spacing: -1.5px;
}

.section-head-modern h2 span {
  color: #ef4444;
}

.section-head-modern p {
  margin: 0;

  color: #94a3b8;

  font-size: 12px;
}

.section-link {
  display: flex;
  align-items: center;

  gap: 8px;

  color: #475569;

  font-size: 10px;

  font-weight: 900;

  letter-spacing: 1px;

  cursor: pointer;

  transition: 0.25s;
}

.section-link i {
  color: #ef4444;

  transition: 0.25s;
}

.section-link:hover {
  color: #ef4444;
}

.section-link:hover i {
  transform: translateX(4px);
}

/* =========================================================
   API PRODUCTS
========================================================= */

.modern-product-grid {
  display: grid;

  grid-template-columns: repeat(5, minmax(0, 1fr));

  gap: 15px;
}

.modern-product-card {
  position: relative;

  overflow: hidden;

  border-radius: 22px;

  background: rgba(255, 255, 255, 0.94);

  border: 1px solid #e9edf3;

  box-shadow: 0 12px 35px rgba(15, 23, 42, 0.055);

  cursor: pointer;

  transition: transform 0.35s cubic-bezier(0.2, 0.8, 0.2, 1),
    box-shadow 0.35s ease, border-color 0.35s ease;
}

.modern-product-card:hover {
  transform: translateY(-9px);

  border-color: #fecdd3;

  box-shadow: 0 25px 55px rgba(15, 23, 42, 0.11);
}

.product-top {
  display: flex;

  align-items: center;
  justify-content: space-between;

  padding: 14px 14px 0;
}

.product-code {
  color: #94a3b8;

  font-size: 9px;

  font-weight: 900;

  letter-spacing: 1px;
}

.status-pill {
  padding: 6px 8px;

  border-radius: 999px;

  font-size: 8px;

  font-weight: 900;
}

.status-active {
  color: #16a34a;

  background: #f0fdf4;

  border: 1px solid #dcfce7;
}

.status-off {
  color: #dc2626;

  background: #fef2f2;

  border: 1px solid #fecaca;
}

.product-image-box {
  position: relative;

  height: 205px;

  margin: 12px 12px 0;

  display: flex;
  align-items: center;
  justify-content: center;

  overflow: hidden;

  border-radius: 18px;

  background: radial-gradient(
      circle at 50% 65%,
      rgba(239, 68, 68, 0.09),
      transparent 62%
    ),
    #f8fafc;
}

.product-image-box::after {
  content: "";

  position: absolute;
  inset: 0;

  border-radius: 18px;

  background: linear-gradient(
    135deg,
    rgba(255, 255, 255, 0.5),
    transparent 35%,
    transparent 65%,
    rgba(239, 68, 68, 0.035)
  );

  pointer-events: none;
}

.product-image-box img {
  width: 88%;
  height: 88%;

  object-fit: contain;

  position: relative;
  z-index: 2;

  filter: drop-shadow(0 18px 20px rgba(15, 23, 42, 0.14));

  transition: transform 0.5s cubic-bezier(0.2, 0.8, 0.2, 1);
}

.modern-product-card:hover .product-image-box img {
  transform: scale(1.07) translateY(-4px);
}

.product-image-glow {
  position: absolute;

  width: 120px;
  height: 80px;

  border-radius: 50%;

  background: radial-gradient(
    ellipse,
    rgba(239, 68, 68, 0.16),
    transparent 70%
  );

  filter: blur(18px);
}

.floating-like {
  position: absolute;

  right: 10px;
  top: 10px;

  width: 31px;
  height: 31px;

  display: flex;
  justify-content: center;
  align-items: center;

  border-radius: 10px;

  color: #94a3b8;

  background: rgba(255, 255, 255, 0.78);

  backdrop-filter: blur(9px);

  border: 1px solid rgba(255, 255, 255, 0.95);

  z-index: 4;
}

.product-info {
  padding: 15px;
}

.product-type {
  color: #cbd5e1;

  font-size: 8px;

  font-weight: 900;

  letter-spacing: 1.7px;
}

.product-info h3 {
  margin: 6px 0 12px;

  min-height: 43px;

  color: #111827;

  font-size: 14px;

  line-height: 1.45;

  font-weight: 800;
}

.product-meta {
  display: flex;

  gap: 6px;

  flex-wrap: wrap;
}

.product-meta span {
  padding: 7px 8px;

  border-radius: 9px;

  color: #64748b;

  background: #f8fafc;

  font-size: 8px;
}

.product-meta b {
  color: #334155;
}

.product-pricing {
  display: flex;

  justify-content: space-between;
  align-items: flex-end;

  gap: 8px;

  margin-top: 15px;
}

.price-label {
  color: #94a3b8;

  font-size: 9px;
}

.product-pricing strong {
  color: #ef4444;

  font-size: 18px;

  font-weight: 900;
}

.product-cart-btn {
  width: 100%;

  margin-top: 14px;

  border: none;

  display: flex;

  align-items: center;
  justify-content: space-between;

  padding: 11px 13px;

  border-radius: 12px;

  color: white;

  background: linear-gradient(135deg, #ef4444, #f5576c);

  font-size: 9px;

  font-weight: 900;

  letter-spacing: 0.8px;

  box-shadow: 0 8px 18px rgba(239, 68, 68, 0.18);

  cursor: pointer;

  transition: 0.25s;
}

.product-cart-btn:hover {
  box-shadow: 0 13px 25px rgba(239, 68, 68, 0.28);

  transform: translateY(-2px);
}

.product-cart-btn i {
  font-size: 12px;
}

/* =========================================================
   BANNERS
========================================================= */

.banner-row-section {
  margin-top: 65px;

  display: grid;

  grid-template-columns: repeat(3, 1fr);

  gap: 15px;
}

.banner-card-modern {
  position: relative;

  overflow: hidden;

  min-height: 175px;

  border-radius: 22px;

  background: white;

  box-shadow: 0 15px 38px rgba(15, 23, 42, 0.07);
}

.banner-card-modern img {
  width: 100%;
  height: 100%;

  object-fit: cover;

  display: block;

  transition: 0.7s;
}

.banner-card-modern:hover img {
  transform: scale(1.07);
}

.banner-shine {
  position: absolute;

  width: 90px;
  height: 150%;

  left: -120%;

  top: -25%;

  transform: rotate(18deg);

  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.35),
    transparent
  );

  transition: 0.9s;
}

.banner-card-modern:hover .banner-shine {
  left: 150%;
}

/* =========================================================
   FAKE PC
========================================================= */

.fake-pc-section {
  margin-top: 70px;
}

.fake-pc-grid {
  display: grid;

  grid-template-columns: repeat(4, 1fr);

  gap: 16px;
}

.fake-pc-card {
  overflow: hidden;

  border-radius: 24px;

  background: #fff;

  border: 1px solid #e9edf3;

  box-shadow: 0 12px 38px rgba(15, 23, 42, 0.05);

  transition: transform 0.35s ease, box-shadow 0.35s ease;
}

.fake-pc-card:hover {
  transform: translateY(-9px);

  box-shadow: 0 25px 55px rgba(15, 23, 42, 0.1);
}

.fake-pc-image {
  position: relative;

  height: 225px;

  display: flex;
  align-items: center;
  justify-content: center;

  overflow: hidden;

  background: radial-gradient(circle, rgba(239, 68, 68, 0.08), transparent 65%),
    #f8fafc;
}

.fake-pc-image img {
  width: 90%;
  height: 90%;

  object-fit: contain;

  position: relative;

  z-index: 2;

  filter: drop-shadow(0 20px 24px rgba(15, 23, 42, 0.16));

  transition: 0.45s;
}

.fake-pc-card:hover .fake-pc-image img {
  transform: scale(1.06) translateY(-5px);
}

.fake-pc-orb {
  position: absolute;

  width: 140px;
  height: 90px;

  border-radius: 50%;

  background: radial-gradient(
    ellipse,
    rgba(239, 68, 68, 0.16),
    transparent 70%
  );

  filter: blur(20px);
}

.fake-pc-badge {
  position: absolute;

  left: 13px;
  top: 13px;

  padding: 6px 8px;

  border-radius: 999px;

  color: #ef4444;

  background: #fff;

  border: 1px solid #fecdd3;

  font-size: 8px;

  font-weight: 900;

  letter-spacing: 1px;

  z-index: 3;
}

.fake-pc-content {
  padding: 16px;
}

.fake-pc-rating {
  display: flex;

  justify-content: space-between;

  color: #94a3b8;

  font-size: 9px;
}

.fake-pc-rating span:first-child {
  color: #f59e0b;

  font-weight: 800;
}

.fake-pc-content h3 {
  margin: 10px 0 5px;

  font-size: 16px;

  font-weight: 900;

  color: #111827;
}

.fake-pc-content p {
  min-height: 38px;

  margin: 0;

  color: #94a3b8;

  font-size: 10px;

  line-height: 1.5;
}

.fake-pc-price {
  margin-top: 14px;

  color: #ef4444;

  font-size: 21px;

  font-weight: 900;
}

/* =========================================================
   LOGIN MODAL
========================================================= */

/* Keep the login dialog above animated homepage layers. */
#loginModal {
  z-index: 200001 !important;
}
.modal-backdrop {
  z-index: 200000 !important;
}
#loginModal .modal-dialog,
#loginModal .modal-content,
#loginModal .modal-footer,
#loginModal .modal-btn-primary,
#loginModal .modal-btn-secondary {
  pointer-events: auto;
}

.modern-login-modal {
  border: none;

  border-radius: 28px;

  overflow: hidden;

  background: #fff;

  box-shadow: 0 35px 90px rgba(15, 23, 42, 0.25);
}

.login-modal-top {
  position: relative;

  height: 125px;

  display: flex;
  align-items: center;
  justify-content: center;

  background: radial-gradient(
      circle at 50% 25%,
      rgba(255, 255, 255, 0.24),
      transparent 30%
    ),
    linear-gradient(135deg, #ef4444, #f5576c);
}

.login-modal-icon {
  width: 66px;
  height: 66px;

  display: flex;
  align-items: center;
  justify-content: center;

  border-radius: 22px;

  color: white;

  font-size: 24px;

  background: rgba(255, 255, 255, 0.14);

  border: 1px solid rgba(255, 255, 255, 0.25);

  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2);
}

.modern-close {
  position: absolute;

  right: 16px;
  top: 16px;

  width: 38px;
  height: 38px;

  display: flex;
  align-items: center;
  justify-content: center;

  border: none;

  border-radius: 12px;

  color: white;

  background: rgba(255, 255, 255, 0.14);

  cursor: pointer;
}

.modal-kicker {
  color: #ef4444;

  font-size: 9px;

  font-weight: 900;

  letter-spacing: 2px;
}

.modern-login-modal h3 {
  margin: 10px 0 8px;

  font-size: 26px;

  font-weight: 900;

  color: #111827;
}

.modern-login-modal p {
  max-width: 380px;

  margin: 0 auto;

  color: #64748b;

  font-size: 13px;

  line-height: 1.7;
}

.modern-modal-footer {
  display: flex;

  justify-content: center;

  gap: 10px;

  padding: 18px 22px 22px;

  border-top: none;
}

.modal-btn-secondary,
.modal-btn-primary {
  border: none;

  border-radius: 12px;

  padding: 11px 15px;

  font-size: 10px;

  font-weight: 900;

  cursor: pointer;
}

.modal-btn-secondary {
  color: #64748b;

  background: #f1f5f9;
}

.modal-btn-primary {
  color: white;

  display: flex;

  align-items: center;

  gap: 10px;

  background: linear-gradient(135deg, #ef4444, #f5576c);

  box-shadow: 0 10px 22px rgba(239, 68, 68, 0.22);
}

/* =========================================================
   CHAT FLOAT
========================================================= */

.chat-float {
  position: fixed;

  right: 26px;
  bottom: 26px;

  width: 68px;
  height: 68px;

  display: flex;
  align-items: center;
  justify-content: center;

  border-radius: 24px;

  color: #fff;

  background: linear-gradient(135deg, #ef4444, #ff5b67);

  font-size: 23px;

  cursor: pointer;

  z-index: 99999;

  box-shadow: 0 20px 40px rgba(239, 68, 68, 0.3),
    0 0 0 1px rgba(255, 255, 255, 0.25);

  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.chat-float:hover {
  transform: translateY(-5px) scale(1.04);

  box-shadow: 0 25px 50px rgba(239, 68, 68, 0.38);
}

.chat-pulse {
  position: absolute;

  inset: -7px;

  border-radius: 30px;

  border: 1px solid rgba(239, 68, 68, 0.25);

  animation: chatPulse 2.2s infinite;
}

@keyframes chatPulse {
  0% {
    transform: scale(0.9);

    opacity: 0.9;
  }

  70% {
    transform: scale(1.18);

    opacity: 0;
  }

  100% {
    opacity: 0;
  }
}

/* =========================================================
   CHAT WRAPPER
========================================================= */

.chat-wrapper {
  position: fixed;

  right: 25px;
  bottom: 108px;

  width: 390px;
  height: 650px;

  display: flex;
  flex-direction: column;

  overflow: hidden;

  border-radius: 28px;

  background: rgba(255, 255, 255, 0.88);

  border: 1px solid rgba(255, 255, 255, 0.8);

  backdrop-filter: blur(20px);

  z-index: 99999;

  box-shadow: 0 35px 100px rgba(15, 23, 42, 0.2);

  animation: showChat 0.28s cubic-bezier(0.2, 0.8, 0.2, 1);
}

@keyframes showChat {
  from {
    opacity: 0;

    transform: translateY(20px) scale(0.97);
  }

  to {
    opacity: 1;

    transform: translateY(0) scale(1);
  }
}

/* =========================================================
   CHAT TOP
========================================================= */

.chat-top {
  position: relative;

  padding: 18px;

  display: flex;

  justify-content: space-between;

  align-items: center;

  color: #fff;

  background: radial-gradient(
      circle at 100% 0%,
      rgba(255, 255, 255, 0.18),
      transparent 30%
    ),
    linear-gradient(135deg, #ef4444, #f5576c);
}

.chat-profile {
  display: flex;
  align-items: center;
}

.avatar {
  position: relative;

  width: 50px;
  height: 50px;

  flex-shrink: 0;

  margin-right: 12px;

  display: flex;
  align-items: center;
  justify-content: center;

  border-radius: 16px;

  color: white;

  background: rgba(255, 255, 255, 0.12);

  border: 1px solid rgba(255, 255, 255, 0.2);

  font-size: 21px;

  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.18);
}

.avatar-ring {
  position: absolute;

  inset: -4px;

  border-radius: 19px;

  border: 1px solid rgba(255, 255, 255, 0.2);

  animation: avatarRing 3s linear infinite;
}

@keyframes avatarRing {
  to {
    transform: rotate(360deg);
  }
}

.chat-profile h4 {
  margin: 0;

  font-size: 17px;

  font-weight: 900;
}

.chat-profile p {
  margin: 3px 0 0;

  color: rgba(255, 255, 255, 0.72);

  font-size: 11px;
}

.chat-name-row {
  display: flex;

  align-items: center;

  gap: 7px;
}

.online-dot {
  width: 7px;
  height: 7px;

  border-radius: 50%;

  background: #86efac;

  box-shadow: 0 0 12px rgba(134, 239, 172, 0.9);
}

.close-btn {
  width: 39px;
  height: 39px;

  display: flex;
  align-items: center;
  justify-content: center;

  border: none;

  border-radius: 12px;

  color: white;

  background: rgba(255, 255, 255, 0.12);

  cursor: pointer;

  transition: 0.25s;
}

.close-btn:hover {
  background: rgba(255, 255, 255, 0.22);

  transform: rotate(90deg);
}

/* =========================================================
   CHAT BODY
========================================================= */

.chat-content {
  flex: 1;

  overflow-y: auto;

  padding: 19px;

  background: radial-gradient(
      circle at 20% 0%,
      rgba(239, 68, 68, 0.035),
      transparent 22%
    ),
    #f8fafc;

  scrollbar-width: thin;
}

.bot-chat,
.user-chat {
  width: 100%;

  display: flex;

  margin-bottom: 16px;
}

.user-chat {
  justify-content: flex-end;
}

.chat-avatar-small {
  width: 34px;
  height: 34px;

  flex-shrink: 0;

  display: flex;
  align-items: center;
  justify-content: center;

  margin-right: 9px;

  border-radius: 11px;

  color: #fff;

  background: linear-gradient(135deg, #ef4444, #f5576c);

  font-size: 12px;

  box-shadow: 0 7px 15px rgba(239, 68, 68, 0.18);
}

.chat-bubble {
  max-width: 76%;

  padding: 12px 14px;

  border-radius: 17px;

  font-size: 13px;

  line-height: 1.6;

  word-break: break-word;
}

.chat-bubble.bot {
  border: 1px solid #edf0f5;

  border-bottom-left-radius: 6px;

  color: #334155;

  background: #fff;

  box-shadow: 0 8px 22px rgba(15, 23, 42, 0.05);
}

.chat-bubble.user {
  border-bottom-right-radius: 6px;

  color: white;

  background: linear-gradient(135deg, #ef4444, #f5576c);

  box-shadow: 0 10px 20px rgba(239, 68, 68, 0.16);
}

/* =========================================================
   CHAT BOTTOM
========================================================= */

.chat-bottom {
  display: flex;

  align-items: center;

  gap: 8px;

  padding: 12px;

  background: #fff;

  border-top: 1px solid #eef2f7;
}

.chat-bottom input {
  flex: 1;

  height: 48px;

  border: 1px solid #edf0f5;

  border-radius: 15px;

  padding: 0 15px;

  outline: none;

  color: #111827;

  background: #f8fafc;

  font-size: 12px;

  transition: 0.25s;
}

.chat-bottom input::placeholder {
  color: #9ca3af;
}

.chat-bottom input:focus {
  background: #fff;

  border-color: #fecaca;

  box-shadow: 0 0 0 4px rgba(239, 68, 68, 0.07);
}

.icon-btn,
.send-btn {
  width: 48px;
  height: 48px;

  flex-shrink: 0;

  display: flex;
  align-items: center;
  justify-content: center;

  border: none;

  border-radius: 15px;

  cursor: pointer;
}

.icon-btn {
  color: #64748b;

  background: #f1f5f9;
}

.send-btn {
  color: #fff;

  background: linear-gradient(135deg, #ef4444, #f5576c);

  box-shadow: 0 8px 18px rgba(239, 68, 68, 0.2);

  transition: 0.25s;
}

.send-btn:hover {
  transform: translateY(-2px) scale(1.03);
}

/* =========================================================
   SCROLLBAR
========================================================= */

.chat-content::-webkit-scrollbar {
  width: 5px;
}

.chat-content::-webkit-scrollbar-thumb {
  border-radius: 99px;

  background: #cbd5e1;
}

.chat-content::-webkit-scrollbar-track {
  background: transparent;
}

/* =========================================================
   RESPONSIVE
========================================================= */

@media (max-width: 1200px) {
  .hero-layout {
    grid-template-columns: 1fr 220px;
  }

  .showroom {
    grid-template-columns: 38% 62%;

    padding: 45px;
  }

  .modern-product-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}

@media (max-width: 991px) {
  .hero-layout {
    grid-template-columns: 1fr;
  }

  .hero-side {
    grid-template-columns: repeat(3, 1fr);

    grid-template-rows: none;
  }

  .hero-promo-card {
    height: 180px;
  }

  .service-strip {
    grid-template-columns: repeat(2, 1fr);
  }

  .category-layout {
    grid-template-columns: 1fr;
  }

  .category-list {
    display: grid;

    grid-template-columns: repeat(2, 1fr);

    gap: 4px;
  }

  .showroom {
    grid-template-columns: 1fr;

    padding: 45px 35px;
  }

  .showroom-info {
    max-width: none;
  }

  .showroom-scene {
    min-height: 500px;

    margin-top: 15px;
  }

  .modern-product-grid {
    grid-template-columns: repeat(3, 1fr);
  }

  .fake-pc-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 768px) {
  .hero-zone {
    padding-top: 12px;
  }

  .hero-image-wrap,
  .hero-main-image {
    min-height: 430px;
  }

  .hero-content {
    left: 28px;
    right: 20px;
  }

  .hero-content h1 {
    font-size: 50px;
    letter-spacing: -2px;
  }

  .hero-floating-chip {
    display: none;
  }

  .hero-side {
    grid-template-columns: repeat(2, 1fr);
  }

  .hero-promo-card {
    height: 145px;
  }

  .hero-promo-card:last-child {
    display: none;
  }

  .section-head-modern {
    align-items: flex-start;
    flex-direction: column;
  }

  .modern-product-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .banner-row-section {
    grid-template-columns: 1fr;
  }

  .banner-card-modern {
    min-height: 150px;
  }

  .showroom {
    min-height: 850px;

    border-radius: 28px;
  }

  .showroom-scene {
    transform: scale(0.83);

    margin-left: -8%;
    margin-right: -8%;
  }

  .showroom-info h2 {
    font-size: 44px;
  }

  .pc-orbit {
    width: 360px;
    height: 360px;
  }
}

@media (max-width: 576px) {
  .hero-content h1 {
    font-size: 40px;
  }

  .hero-content p {
    font-size: 13px;
  }

  .hero-btn {
    padding: 12px 14px;
    font-size: 10px;
  }

  .service-strip {
    grid-template-columns: 1fr;
  }

  .category-list {
    grid-template-columns: 1fr;
  }

  .promo-small-row {
    grid-template-columns: 1fr;
  }

  .promo-small {
    min-height: 120px;
  }

  .modern-product-grid {
    grid-template-columns: 1fr 1fr;

    gap: 10px;
  }

  .modern-product-card {
    border-radius: 18px;
  }

  .product-image-box {
    height: 170px;
  }

  .product-info {
    padding: 12px;
  }

  .product-pricing strong {
    font-size: 15px;
  }

  .product-cart-btn {
    font-size: 8px;
  }

  .fake-pc-grid {
    grid-template-columns: 1fr;
  }

  .showroom {
    padding: 34px 18px;

    min-height: 820px;
  }

  .showroom-info h2 {
    font-size: 38px;
  }

  .showroom-scene {
    transform: scale(0.68);

    margin-top: -20px;
    margin-left: -24%;
    margin-right: -24%;
  }

  .chat-wrapper {
    width: calc(100vw - 20px);

    right: 10px;

    bottom: 92px;

    height: min(650px, 80vh);

    border-radius: 24px;
  }

  .chat-float {
    right: 15px;
    bottom: 15px;

    width: 60px;
    height: 60px;

    border-radius: 20px;
  }

  .modern-modal-footer {
    flex-direction: column;
  }

  .modal-btn-secondary,
  .modal-btn-primary {
    width: 100%;

    justify-content: center;
  }
}

@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.001ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.001ms !important;
  }
}

/* =========================================================
   TECHSHOP PREMIUM MOTION LAYER
   Tăng chiều sâu và tương tác nhưng vẫn giữ nguyên cấu trúc/API.
========================================================= */
.home-page {
  --tech-red: #ff334f;
  --tech-orange: #ff7657;
  --tech-blue: #59d8ff;
  isolation: isolate;
  background: radial-gradient(
      ellipse at 5% 5%,
      rgba(255, 51, 79, 0.1),
      transparent 27%
    ),
    radial-gradient(
      ellipse at 95% 35%,
      rgba(89, 216, 255, 0.08),
      transparent 28%
    ),
    linear-gradient(180deg, #f8fafc 0%, #f3f5fa 52%, #f8fafc 100%);
}
.home-page::before {
  width: 620px;
  height: 620px;
  left: auto;
  right: -320px;
  top: 42%;
  background: radial-gradient(
    circle,
    rgba(89, 216, 255, 0.08),
    transparent 68%
  );
  animation: ambientDrift 15s ease-in-out infinite alternate;
}
@keyframes ambientDrift {
  from {
    transform: translate3d(0, -25px, 0) scale(0.92);
  }
  to {
    transform: translate3d(-35px, 35px, 0) scale(1.08);
  }
}
.hero-image-wrap {
  border: 1px solid rgba(255, 255, 255, 0.18);
  box-shadow: 0 30px 85px rgba(5, 10, 25, 0.24),
    0 0 0 1px rgba(255, 255, 255, 0.22) inset;
  animation: heroEnter 0.9s cubic-bezier(0.2, 0.8, 0.2, 1) both;
}
@keyframes heroEnter {
  from {
    opacity: 0;
    transform: translateY(22px) scale(0.985);
    filter: blur(5px);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
    filter: blur(0);
  }
}
.hero-main-image {
  animation: heroImageBreathe 18s ease-in-out infinite alternate;
}
@keyframes heroImageBreathe {
  from {
    transform: scale(1.015);
  }
  to {
    transform: scale(1.075);
  }
}
.hero-image-wrap:hover .hero-main-image {
  transform: scale(1.09);
}
.hero-content {
  animation: heroCopyEnter 0.9s 0.12s both;
}
@keyframes heroCopyEnter {
  from {
    opacity: 0;
    transform: translate(-14px, calc(-50% + 10px));
  }
  to {
    opacity: 1;
    transform: translate(0, -50%);
  }
}
.hero-content h1 span {
  background-size: 200% auto;
  animation: titleGradient 5s linear infinite;
  filter: drop-shadow(0 8px 22px rgba(255, 51, 79, 0.16));
}
@keyframes titleGradient {
  to {
    background-position: 200% center;
  }
}
.hero-btn {
  position: relative;
  overflow: hidden;
  isolation: isolate;
}
.hero-btn::before {
  content: "";
  position: absolute;
  inset: -60% auto -60% -40%;
  width: 32%;
  transform: rotate(24deg);
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.55),
    transparent
  );
  animation: buttonSweep 4.2s ease-in-out infinite;
  z-index: -1;
}
@keyframes buttonSweep {
  0%,
  55% {
    left: -45%;
  }
  80%,
  100% {
    left: 130%;
  }
}
.hero-promo-card {
  transform: translateZ(0);
  transition: transform 0.45s cubic-bezier(0.2, 0.8, 0.2, 1), box-shadow 0.45s;
}
.hero-promo-card:hover {
  transform: translateY(-5px) rotateX(2deg);
  box-shadow: 0 24px 55px rgba(15, 23, 42, 0.18);
}
.service-card {
  position: relative;
  overflow: hidden;
}
.service-card::after {
  content: "";
  position: absolute;
  inset: 0;
  pointer-events: none;
  opacity: 0;
  background: linear-gradient(
    115deg,
    transparent 20%,
    rgba(255, 255, 255, 0.9) 48%,
    transparent 75%
  );
  transform: translateX(-110%);
  transition: opacity 0.3s;
}
.service-card:hover::after {
  opacity: 1;
  animation: serviceShimmer 0.85s ease;
}
@keyframes serviceShimmer {
  to {
    transform: translateX(110%);
  }
}
.category-panel {
  box-shadow: 0 25px 65px rgba(5, 10, 25, 0.23),
    0 0 0 1px rgba(255, 255, 255, 0.06) inset;
}
.category-item {
  position: relative;
  overflow: hidden;
}
.category-item::before {
  content: "";
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  width: 2px;
  background: linear-gradient(180deg, var(--tech-red), var(--tech-orange));
  transform: scaleY(0);
  transition: transform 0.25s ease;
}
.category-item:hover::before,
.category-item.active::before {
  transform: scaleY(1);
}
.showroom {
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 35px 100px rgba(2, 6, 23, 0.25),
    0 0 50px rgba(239, 68, 68, 0.05);
}
.showroom::after {
  content: "";
  position: absolute;
  inset: 0;
  z-index: 1;
  pointer-events: none;
  background: linear-gradient(
    115deg,
    transparent 20%,
    rgba(255, 255, 255, 0.045) 48%,
    transparent 72%
  );
  background-size: 220% 100%;
  animation: showroomSweep 9s ease-in-out infinite;
}
@keyframes showroomSweep {
  0%,
  35% {
    background-position: 140% 0;
  }
  70%,
  100% {
    background-position: -120% 0;
  }
}
.pc-3d-card {
  backface-visibility: visible;
}
.pc-3d-inner {
  position: relative;
  overflow: hidden;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.13),
    0 30px 65px rgba(0, 0, 0, 0.32);
}
.pc-3d-inner::after {
  content: "";
  position: absolute;
  inset: 0;
  pointer-events: none;
  border-radius: inherit;
  background: linear-gradient(
    135deg,
    rgba(255, 255, 255, 0.1),
    transparent 32%,
    transparent 70%,
    rgba(89, 216, 255, 0.07)
  );
}
.pc-3d-card:hover .pc-3d-inner {
  border-color: rgba(255, 89, 109, 0.55);
  box-shadow: 0 0 34px rgba(255, 51, 79, 0.18),
    inset 0 1px 0 rgba(255, 255, 255, 0.15);
}
.pc-image-wrap img {
  transition: transform 0.55s cubic-bezier(0.2, 0.8, 0.2, 1), filter 0.55s;
}
.pc-3d-card:hover .pc-image-wrap img {
  transform: translateY(-8px) scale(1.08);
  filter: drop-shadow(0 20px 28px rgba(255, 51, 79, 0.2));
}
.scene-ring {
  box-shadow: 0 0 24px rgba(89, 216, 255, 0.04);
}
.core-center {
  animation: coreBreath 3.8s ease-in-out infinite;
}
@keyframes coreBreath {
  0%,
  100% {
    box-shadow: 0 0 30px rgba(239, 68, 68, 0.15),
      inset 0 0 25px rgba(255, 255, 255, 0.05);
  }
  50% {
    box-shadow: 0 0 55px rgba(89, 216, 255, 0.2),
      inset 0 0 30px rgba(255, 255, 255, 0.1);
  }
}
.section-head-modern {
  position: relative;
}
.section-head-modern h2 {
  letter-spacing: -1.8px;
}
.section-kicker {
  display: inline-flex;
  align-items: center;
  gap: 9px;
}
.section-kicker::before {
  content: "";
  width: 22px;
  height: 2px;
  border-radius: 9px;
  background: linear-gradient(90deg, var(--tech-red), var(--tech-orange));
  box-shadow: 0 0 12px rgba(255, 51, 79, 0.32);
}
.section-link-button {
  border: 0;
  background: transparent;
  padding: 10px 0 10px 12px;
  font: inherit;
}
.section-link-button:focus-visible {
  outline: 3px solid rgba(255, 51, 79, 0.28);
  outline-offset: 4px;
  border-radius: 8px;
}
.modern-product-grid {
  grid-template-columns: repeat(4, minmax(0, 1fr));
  perspective: 1200px;
}
.modern-product-card {
  --spot-x: 50%;
  --spot-y: 50%;
  isolation: isolate;
  transform-style: preserve-3d;
  border-color: rgba(226, 232, 240, 0.9);
  box-shadow: 0 15px 38px rgba(15, 23, 42, 0.065),
    0 1px 0 rgba(255, 255, 255, 0.9) inset;
  transition: transform 0.18s ease, box-shadow 0.3s ease, border-color 0.3s ease;
}
.modern-product-card::before {
  content: "";
  position: absolute;
  inset: 0;
  z-index: -1;
  pointer-events: none;
  border-radius: inherit;
  background: radial-gradient(
    circle at var(--spot-x) var(--spot-y),
    rgba(255, 51, 79, 0.12),
    transparent 42%
  );
  opacity: 0;
  transition: opacity 0.25s ease;
}
.modern-product-card:hover::before {
  opacity: 1;
}
.modern-product-card:hover {
  border-color: rgba(255, 51, 79, 0.32);
  box-shadow: 0 28px 58px rgba(15, 23, 42, 0.13),
    0 0 0 1px rgba(255, 51, 79, 0.04);
}
.product-image-box {
  background: radial-gradient(
      circle at 50% 55%,
      rgba(255, 51, 79, 0.12),
      transparent 56%
    ),
    linear-gradient(145deg, #fff, #f1f5f9);
}
.product-image-box img {
  transform: translateZ(24px);
  transition: transform 0.45s cubic-bezier(0.2, 0.8, 0.2, 1), filter 0.45s;
}
.modern-product-card:hover .product-image-box img {
  transform: translateZ(35px) scale(1.1) rotate(-1.5deg);
  filter: drop-shadow(0 22px 23px rgba(15, 23, 42, 0.18));
}
.product-image-glow {
  animation: productGlow 4s ease-in-out infinite alternate;
}
@keyframes productGlow {
  from {
    transform: scale(0.85);
    opacity: 0.55;
  }
  to {
    transform: scale(1.2);
    opacity: 1;
  }
}
.product-cart-btn {
  position: relative;
  overflow: hidden;
}
.product-cart-btn::before {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(
    110deg,
    transparent 25%,
    rgba(255, 255, 255, 0.23) 50%,
    transparent 75%
  );
  transform: translateX(-110%);
  transition: transform 0.65s ease;
}
.product-cart-btn:hover::before {
  transform: translateX(110%);
}
.product-cart-btn i {
  transition: transform 0.25s ease;
}
.product-cart-btn:hover i {
  transform: translateX(-3px) rotate(-8deg);
}
.banner-card-modern {
  border: 1px solid rgba(255, 255, 255, 0.8);
  box-shadow: 0 18px 45px rgba(15, 23, 42, 0.1);
}
.banner-card-modern:hover {
  transform: translateY(-5px);
  box-shadow: 0 26px 60px rgba(15, 23, 42, 0.14);
}
.chat-float {
  animation: chatEntrance 0.7s 0.8s both, chatIdle 4s 1.5s ease-in-out infinite;
}
@keyframes chatEntrance {
  from {
    opacity: 0;
    transform: translateY(16px) scale(0.8);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}
@keyframes chatIdle {
  0%,
  100% {
    box-shadow: 0 20px 40px rgba(239, 68, 68, 0.3),
      0 0 0 1px rgba(255, 255, 255, 0.25);
  }
  50% {
    box-shadow: 0 22px 48px rgba(239, 68, 68, 0.42),
      0 0 0 1px rgba(255, 255, 255, 0.32);
  }
}
.chat-wrapper {
  border-color: rgba(255, 255, 255, 0.95);
  box-shadow: 0 35px 100px rgba(15, 23, 42, 0.24),
    0 0 0 1px rgba(15, 23, 42, 0.03);
}

@media (max-width: 991px) {
  .modern-product-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}
@media (max-width: 576px) {
  .modern-product-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 10px;
  }
  .product-image-box {
    height: 155px;
  }
  .product-top {
    gap: 5px;
    padding: 11px 10px 0;
  }
  .status-pill {
    font-size: 7px;
    padding: 5px 6px;
  }
  .product-info h3 {
    font-size: 12px;
    min-height: 52px;
  }
  .product-meta span {
    font-size: 7px;
  }
  .product-pricing {
    align-items: flex-start;
    flex-direction: column;
    gap: 3px;
  }
  .product-pricing strong {
    font-size: 14px;
  }
  .section-head-modern h2 {
    font-size: 29px;
  }
}
@media (prefers-reduced-motion: reduce) {
  .hero-main-image,
  .hero-content,
  .hero-image-wrap,
  .home-page::before,
  .hero-content h1 span,
  .hero-btn::before,
  .showroom::after,
  .core-center,
  .product-image-glow,
  .chat-float {
    animation: none !important;
  }
}

/* =========================================================
   TECHSHOP AI CHAT — RESPONSIVE REDESIGN
   Tách riêng để không ảnh hưởng layout trang chủ.
========================================================= */
.chat-float {
  position: fixed;
  right: 24px;
  bottom: 24px;
  z-index: 100001;
  width: 60px;
  height: 60px;
  padding: 0;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  border: 0;
  border-radius: 20px;
  color: #fff;
  background: linear-gradient(135deg, #2563eb 0%, #4f46e5 100%);
  font-size: 22px;
  cursor: pointer;
  box-shadow: 0 14px 34px rgba(37, 99, 235, 0.32),
    inset 0 1px 0 rgba(255, 255, 255, 0.25);
  transition: transform 0.22s ease, box-shadow 0.22s ease;
}
.chat-float:hover {
  transform: translateY(-3px);
  box-shadow: 0 18px 40px rgba(37, 99, 235, 0.4);
}
.chat-float:focus-visible,
.chat-wrapper button:focus-visible,
.chat-bottom input:focus-visible {
  outline: 3px solid rgba(96, 165, 250, 0.75);
  outline-offset: 3px;
}
.chat-float-open {
  background: #172554;
}
.chat-pulse {
  border-color: rgba(59, 130, 246, 0.35);
  border-radius: 23px;
}

.chat-wrapper {
  position: fixed;
  z-index: 100000;
  right: 24px;
  bottom: 98px;
  width: min(420px, calc(100vw - 32px));
  height: min(650px, calc(100dvh - 124px));
  min-height: 360px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  border: 1px solid rgba(226, 232, 240, 0.92);
  border-radius: 24px;
  background: #fff;
  box-shadow: 0 28px 85px rgba(15, 23, 42, 0.25),
    0 5px 18px rgba(15, 23, 42, 0.08);
  isolation: isolate;
}
.chat-top {
  flex: 0 0 auto;
  min-height: 94px;
  padding: 17px 18px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  color: #fff;
  background: radial-gradient(
      circle at 100% 0%,
      rgba(96, 165, 250, 0.28),
      transparent 38%
    ),
    linear-gradient(125deg, #101d3b 0%, #1e3a8a 58%, #4338ca 100%);
}
.chat-profile {
  min-width: 0;
  display: flex;
  align-items: center;
}
.avatar {
  position: relative;
  width: 47px;
  height: 47px;
  margin-right: 12px;
  flex: 0 0 47px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid rgba(255, 255, 255, 0.24);
  border-radius: 15px;
  color: #fff;
  background: rgba(255, 255, 255, 0.13);
  font-size: 20px;
}
.avatar-ring {
  position: absolute;
  inset: -4px;
  border: 1px solid rgba(191, 219, 254, 0.38);
  border-radius: 18px;
  pointer-events: none;
}
.chat-profile-copy {
  min-width: 0;
}
.chat-profile h4 {
  margin: 0;
  color: #fff;
  font-size: 16px;
  font-weight: 800;
  letter-spacing: -0.2px;
}
.chat-profile p {
  margin: 4px 0 3px;
  color: rgba(255, 255, 255, 0.77);
  font-size: 11px;
}
.chat-name-row {
  display: flex;
  align-items: center;
  gap: 8px;
}
.online-dot {
  width: 7px;
  height: 7px;
  flex: 0 0 7px;
  border-radius: 50%;
  background: #4ade80;
  box-shadow: 0 0 0 4px rgba(74, 222, 128, 0.12);
}
.chat-powered {
  color: #bfdbfe;
  font-size: 10px;
  line-height: 1.2;
}
.chat-powered i {
  margin-right: 3px;
}
.close-btn {
  width: 36px;
  height: 36px;
  flex: 0 0 36px;
  border: 1px solid rgba(255, 255, 255, 0.17);
  border-radius: 12px;
  color: #fff;
  background: rgba(255, 255, 255, 0.1);
  cursor: pointer;
  transition: background 0.2s ease, transform 0.2s ease;
}
.close-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: rotate(90deg);
}

.chat-content {
  flex: 1 1 auto;
  min-height: 0;
  overflow-x: hidden;
  overflow-y: auto;
  overscroll-behavior: contain;
  padding: 18px 16px 14px;
  background: linear-gradient(180deg, #f8fbff 0%, #f8fafc 100%);
  scrollbar-width: thin;
  scrollbar-color: #cbd5e1 transparent;
}
.chat-intro {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  margin: 0 0 18px;
  padding: 12px;
  border: 1px solid #dbeafe;
  border-radius: 16px;
  background: linear-gradient(135deg, #eff6ff, #f5f3ff);
}
.chat-intro-icon {
  width: 30px;
  height: 30px;
  flex: 0 0 30px;
  display: grid;
  place-items: center;
  border-radius: 10px;
  color: #fff;
  background: linear-gradient(135deg, #2563eb, #6366f1);
}
.chat-intro strong {
  display: block;
  margin: 1px 0 4px;
  color: #172554;
  font-size: 12px;
  line-height: 1.4;
}
.chat-intro p {
  margin: 0;
  color: #64748b;
  font-size: 11px;
  line-height: 1.55;
}
.bot-chat,
.user-chat {
  width: 100%;
  display: flex;
  align-items: flex-end;
  gap: 8px;
  margin: 0 0 13px;
}
.user-chat {
  justify-content: flex-end;
}
.chat-avatar-small {
  width: 28px;
  height: 28px;
  flex: 0 0 28px;
  display: grid;
  place-items: center;
  margin: 0 0 2px;
  border: 1px solid #dbeafe;
  border-radius: 10px;
  color: #2563eb;
  background: #eff6ff;
  font-size: 11px;
}
.chat-bubble {
  max-width: calc(100% - 38px);
  min-width: 0;
  padding: 11px 13px;
  border-radius: 16px;
  font-size: 13px;
  line-height: 1.65;
  white-space: pre-wrap;
  overflow-wrap: anywhere;
  word-break: normal;
}
.chat-bubble.bot {
  color: #334155;
  border: 1px solid #e5eaf2;
  border-bottom-left-radius: 5px;
  background: #fff;
  box-shadow: 0 3px 10px rgba(15, 23, 42, 0.035);
}
.chat-bubble.user {
  max-width: 84%;
  color: #fff;
  border: 1px solid rgba(37, 99, 235, 0.18);
  border-bottom-right-radius: 5px;
  background: linear-gradient(135deg, #2563eb, #4f46e5);
  box-shadow: 0 5px 13px rgba(37, 99, 235, 0.16);
}
.typing-bubble {
  min-height: 39px;
  display: flex;
  align-items: center;
  gap: 4px;
}
.typing-bubble > span {
  width: 5px;
  height: 5px;
  display: inline-block;
  border-radius: 50%;
  background: #64748b;
  animation: techshopTyping 1s ease-in-out infinite;
}
.typing-bubble > span:nth-child(2) {
  animation-delay: 0.15s;
}
.typing-bubble > span:nth-child(3) {
  animation-delay: 0.3s;
}
.typing-bubble small {
  margin-left: 5px;
  color: #64748b;
  font-size: 10px;
}
@keyframes techshopTyping {
  0%,
  60%,
  100% {
    transform: translateY(0);
    opacity: 0.5;
  }
  30% {
    transform: translateY(-4px);
    opacity: 1;
  }
}
.chat-suggestions {
  display: flex;
  flex-wrap: wrap;
  gap: 7px;
  padding: 2px 0 7px 36px;
}
.chat-suggestions > span {
  width: 100%;
  color: #94a3b8;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 0.35px;
  text-transform: uppercase;
}
.chat-suggestions button {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  max-width: 100%;
  padding: 8px 10px;
  border: 1px solid #dbeafe;
  border-radius: 12px;
  color: #1d4ed8;
  background: #fff;
  font-size: 11px;
  line-height: 1.3;
  cursor: pointer;
  transition: border-color 0.2s ease, background 0.2s ease, transform 0.2s ease;
}
.chat-suggestions button:hover {
  transform: translateY(-1px);
  border-color: #93c5fd;
  background: #eff6ff;
}
.chat-content::-webkit-scrollbar {
  width: 5px;
}
.chat-content::-webkit-scrollbar-thumb {
  border-radius: 99px;
  background: #cbd5e1;
}
.chat-content::-webkit-scrollbar-track {
  background: transparent;
}

.chat-bottom {
  flex: 0 0 auto;
  display: flex;
  align-items: flex-end;
  gap: 9px;
  padding: 12px 13px 9px;
  border-top: 1px solid #e9eef5;
  background: #fff;
}
.chat-input-wrap {
  min-width: 0;
  flex: 1;
}
.chat-bottom input {
  width: 100%;
  height: 43px;
  padding: 0 13px;
  border: 1px solid #e2e8f0;
  border-radius: 13px;
  outline: none;
  color: #0f172a;
  background: #f8fafc;
  font-size: 13px;
  transition: background 0.2s ease, border-color 0.2s ease, box-shadow 0.2s ease;
}
.chat-bottom input::placeholder {
  color: #94a3b8;
}
.chat-bottom input:focus {
  border-color: #93c5fd;
  background: #fff;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}
.chat-bottom input:disabled {
  opacity: 0.72;
  cursor: wait;
}
.chat-input-hint {
  display: block;
  margin: 5px 2px 0;
  color: #94a3b8;
  font-size: 9px;
}
.send-btn {
  width: 43px;
  height: 43px;
  flex: 0 0 43px;
  display: grid;
  place-items: center;
  border: 0;
  border-radius: 13px;
  color: #fff;
  background: linear-gradient(135deg, #2563eb, #4f46e5);
  box-shadow: 0 6px 14px rgba(37, 99, 235, 0.22);
  cursor: pointer;
  transition: transform 0.2s ease, opacity 0.2s ease, box-shadow 0.2s ease;
}
.send-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 9px 18px rgba(37, 99, 235, 0.3);
}
.send-btn:disabled {
  opacity: 0.45;
  cursor: not-allowed;
  box-shadow: none;
}
.chat-footer-note {
  flex: 0 0 auto;
  padding: 0 14px 11px;
  color: #94a3b8;
  background: #fff;
  text-align: center;
  font-size: 9px;
}
.chat-footer-note i {
  margin-right: 3px;
  color: #64748b;
}

@media (max-width: 600px) {
  .chat-float {
    right: 16px;
    bottom: 16px;
    width: 56px;
    height: 56px;
    border-radius: 18px;
  }
  .chat-wrapper {
    right: 12px;
    bottom: 84px;
    width: calc(100vw - 24px);
    height: min(620px, calc(100dvh - 102px));
    min-height: 320px;
    border-radius: 21px;
  }
  .chat-top {
    min-height: 83px;
    padding: 13px 14px;
  }
  .avatar {
    width: 42px;
    height: 42px;
    flex-basis: 42px;
    margin-right: 10px;
    border-radius: 13px;
  }
  .chat-profile h4 {
    font-size: 15px;
  }
  .chat-content {
    padding: 13px 11px 10px;
  }
  .chat-intro {
    gap: 8px;
    padding: 10px;
    margin-bottom: 14px;
  }
  .chat-bubble {
    padding: 10px 11px;
    font-size: 12px;
  }
  .chat-suggestions {
    padding-left: 34px;
    gap: 6px;
  }
  .chat-suggestions button {
    padding: 7px 9px;
    font-size: 10px;
  }
  .chat-bottom {
    padding: 10px 10px 7px;
    gap: 7px;
  }
  .chat-bottom input {
    height: 42px;
    font-size: 16px;
  }
  .send-btn {
    width: 42px;
    height: 42px;
    flex-basis: 42px;
  }
  .chat-footer-note {
    padding-bottom: 9px;
    font-size: 8px;
  }
}
@media (max-width: 360px) {
  .chat-wrapper {
    right: 8px;
    width: calc(100vw - 16px);
    bottom: 78px;
    height: calc(100dvh - 94px);
  }
  .chat-top {
    padding: 11px;
  }
  .chat-powered {
    font-size: 9px;
  }
  .chat-intro p {
    font-size: 10px;
  }
}
@media (max-height: 500px) and (min-width: 601px) {
  .chat-wrapper {
    height: calc(100dvh - 105px);
    min-height: 0;
    bottom: 82px;
  }
}
@media (prefers-reduced-motion: reduce) {
  .chat-float,
  .chat-pulse,
  .avatar-ring,
  .typing-bubble > span {
    animation: none !important;
    transition: none !important;
  }
}

/* Nhãn bảo hành trên ảnh sản phẩm */
.product-image-box {
  position: relative;
}

.product-warranty-badge {
  position: absolute;
  top: 12px;
  left: 12px;
  z-index: 5;

  display: inline-flex;
  align-items: center;
  gap: 6px;

  padding: 7px 11px;
  border-radius: 20px;

  background: rgba(15, 23, 42, 0.88);
  border: 1px solid rgba(96, 165, 250, 0.5);

  color: #bfdbfe;
  font-size: 11px;
  font-weight: 700;

  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(8px);
}

.product-warranty-badge i {
  color: #38bdf8;
  font-size: 12px;
}

/* Thương hiệu sản phẩm */
.product-brand {
  display: flex;
  align-items: center;
  gap: 7px;

  margin-bottom: 8px;

  color: #38bdf8;
  font-size: 12px;
  font-weight: 800;
  text-transform: uppercase;
  letter-spacing: 0.7px;
}

.product-brand-logo {
  width: 22px;
  height: 22px;
  object-fit: contain;
  border-radius: 4px;
}


/* NÚT DANH MỤC */
.category-item {
    width: 100%;
    border: none;
    background: transparent;
    font: inherit;
    text-align: left;
}

/* MÃ DANH MỤC */
.category-code {
    flex-shrink: 0;
    color: #94a3b8;
    font-size: 9px;
    font-weight: 700;
    letter-spacing: .4px;
}

/* DANH MỤC ĐANG CHỌN */
.category-item.active .category-code {
    color: #ff596d;
}
















/* =========================================
   TECHSHOP PREMIUM SERVICE SECTION
========================================= */

.service-section {
  margin-top: 32px;
  margin-bottom: 10px;
}

.service-section-heading {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  gap: 20px;
  margin-bottom: 17px;
}

.service-kicker {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  color: #ef4444;
  font-size: 9px;
  font-weight: 900;
  letter-spacing: 1.8px;
}

.service-kicker span {
  width: 20px;
  height: 3px;
  border-radius: 10px;
  background: #ef4444;
}

.service-section-heading h2 {
  margin: 7px 0 0;
  color: #172033;
  font-size: 22px;
  font-weight: 900;
  letter-spacing: -0.8px;
}

.service-section-heading h2 span {
  color: #ef4444;
}

.service-section-heading p {
  max-width: 330px;
  margin: 0 0 3px;
  color: #8b95a7;
  font-size: 11px;
  line-height: 1.7;
  text-align: right;
}

/* GRID */

.service-strip {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 15px;
}

/* CARD */

.service-card {
  --service-color: #ef4444;
  --service-soft: #fff1f2;

  position: relative;
  min-width: 0;
  padding: 20px 18px 15px;
  overflow: hidden;

  border: 1px solid #edf0f5;
  border-radius: 19px;

  background:
    linear-gradient(145deg, #ffffff, #fbfcff);

  box-shadow:
    0 5px 18px rgba(15, 23, 42, 0.035),
    0 1px 3px rgba(15, 23, 42, 0.025);

  transition:
    transform .3s ease,
    box-shadow .3s ease,
    border-color .3s ease;
}

.service-card::before {
  content: "";
  position: absolute;
  top: 0;
  left: 18px;
  right: 18px;
  height: 3px;
  border-radius: 0 0 8px 8px;
  background: linear-gradient(
    90deg,
    var(--service-color),
    transparent
  );
  opacity: .75;
}

.service-card::after {
  content: "";
  position: absolute;
  width: 110px;
  height: 110px;
  right: -45px;
  top: -50px;
  border-radius: 50%;
  background: var(--service-soft);
  opacity: .65;
  pointer-events: none;
}

.service-card:hover {
  transform: translateY(-6px);
  border-color: color-mix(
    in srgb,
    var(--service-color) 30%,
    white
  );
  box-shadow:
    0 18px 38px rgba(15, 23, 42, .08),
    0 5px 12px rgba(15, 23, 42, .035);
}

/* ICON */

.service-card-top {
  position: relative;
  z-index: 1;
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}

.service-icon {
  width: 48px;
  height: 48px;
  display: grid;
  place-items: center;
  border-radius: 15px;

  color: var(--service-color);
  background: var(--service-soft);

  font-size: 21px;

  box-shadow: inset 0 0 0 1px
    color-mix(in srgb, var(--service-color) 9%, transparent);

  transition: transform .3s ease;
}

.service-card:hover .service-icon {
  transform: rotate(-7deg) scale(1.08);
}

.service-number {
  color: #c3cbd8;
  font-size: 11px;
  font-weight: 900;
  letter-spacing: 1px;
}

/* MÀU RIÊNG TỪNG DỊCH VỤ */

.service-delivery {
  --service-color: #2588e8;
  --service-soft: #edf6ff;
}

.service-warranty {
  --service-color: #16a477;
  --service-soft: #eafaf3;
}

.service-support {
  --service-color: #8758df;
  --service-soft: #f3efff;
}

.service-deal {
  --service-color: #ef5058;
  --service-soft: #fff0f0;
}

/* TEXT */

.service-copy {
  position: relative;
  z-index: 1;
  margin-top: 17px;
}

.service-copy strong {
  display: block;
  color: #172033;
  font-size: 14px;
  font-weight: 850;
  letter-spacing: -.3px;
}

.service-copy span {
  display: block;
  min-height: 32px;
  margin-top: 6px;
  color: #8792a4;
  font-size: 10px;
  line-height: 1.65;
}

/* BOTTOM LABEL */

.service-card-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;

  margin-top: 15px;
  padding-top: 12px;

  border-top: 1px solid #f0f2f6;
}

.service-card-bottom span {
  color: var(--service-color);
  font-size: 8px;
  font-weight: 900;
  letter-spacing: 1px;
}

.service-card-bottom i {
  color: #a4adbb;
  font-size: 11px;
  transition: transform .25s, color .25s;
}

.service-card:hover .service-card-bottom i {
  color: var(--service-color);
  transform: translateX(4px);
}

/* RESPONSIVE */

@media (max-width: 992px) {
  .service-strip {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 576px) {
  .service-section {
    margin-top: 24px;
  }

  .service-section-heading {
    align-items: flex-start;
    flex-direction: column;
    gap: 6px;
  }

  .service-section-heading h2 {
    font-size: 20px;
  }

  .service-section-heading p {
    text-align: left;
  }

  .service-strip {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 10px;
  }

  .service-card {
    padding: 15px 12px 12px;
    border-radius: 16px;
  }

  .service-icon {
    width: 41px;
    height: 41px;
    border-radius: 13px;
    font-size: 18px;
  }

  .service-copy strong {
    font-size: 12px;
  }

  .service-copy span {
    font-size: 9px;
  }

  .service-card-bottom span {
    font-size: 7px;
  }
}



/* =========================================
   CHAT: MỞ RỘNG + MICRO + TIN NHẮN ĐẸP HƠN
========================================= */

/* ---- Nút header ---- */
.chat-actions {
  display: flex;
  align-items: center;
  gap: 8px;
}
.expand-btn {
  width: 36px;
  height: 36px;
  flex: 0 0 36px;
  border: 1px solid rgba(255, 255, 255, 0.17);
  border-radius: 12px;
  color: #fff;
  background: rgba(255, 255, 255, 0.1);
  cursor: pointer;
  transition: background 0.2s ease, transform 0.2s ease;
}
.expand-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: scale(1.08);
}

/* ---- Mở rộng khung chat ---- */
.chat-wrapper {
  transition: width 0.3s cubic-bezier(0.2, 0.8, 0.2, 1),
    height 0.3s cubic-bezier(0.2, 0.8, 0.2, 1);
}
.chat-wrapper.chat-expanded {
  width: min(780px, calc(100vw - 48px));
  height: min(860px, calc(100dvh - 124px));
}
.chat-expanded .chat-content {
  padding: 24px 28px 18px;
}
.chat-expanded .chat-bubble {
  font-size: 14px;
  max-width: min(78%, 560px);
}
.chat-expanded .chat-bubble.user {
  max-width: min(72%, 520px);
}
.chat-expanded .chat-bottom {
  padding: 14px 20px 10px;
}
.chat-expanded .chat-bottom input {
  height: 48px;
  font-size: 14px;
}
.chat-expanded .send-btn {
  width: 48px;
  height: 48px;
  flex-basis: 48px;
}

/* ---- Tin nhắn đẹp hơn ---- */
.bot-chat,
.user-chat {
  animation: msgIn 0.28s cubic-bezier(0.2, 0.8, 0.2, 1) both;
}
@keyframes msgIn {
  from {
    opacity: 0;
    transform: translateY(10px) scale(0.98);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}
.chat-avatar-small {
  color: #fff;
  border: none;
  background: linear-gradient(135deg, #2563eb, #6366f1);
  box-shadow: 0 5px 12px rgba(79, 70, 229, 0.28);
}
.chat-bubble {
  padding: 12px 15px;
  line-height: 1.7;
  letter-spacing: 0.1px;
}
.chat-bubble.bot {
  border: 1px solid #e3e9f5;
  border-left: 3px solid #6366f1;
  border-radius: 6px 18px 18px 18px;
  color: #1e293b;
  background: linear-gradient(180deg, #ffffff, #f8faff);
  box-shadow: 0 6px 18px rgba(30, 41, 59, 0.06);
}
.chat-bubble.user {
  border: none;
  border-radius: 18px 6px 18px 18px;
  background: linear-gradient(135deg, #2563eb 0%, #4f46e5 60%, #7c3aed 100%);
  box-shadow: 0 8px 20px rgba(79, 70, 229, 0.28);
}
.chat-content {
  background: radial-gradient(
      circle at 15% 0%,
      rgba(99, 102, 241, 0.07),
      transparent 30%
    ),
    radial-gradient(circle at 90% 100%, rgba(59, 130, 246, 0.06), transparent 30%),
    #f8faff;
}

/* ---- Micro ---- */
.chat-input-field {
  position: relative;
}
.chat-bottom input {
  padding-right: 48px;
}
.mic-btn {
  position: absolute;
  right: 5px;
  top: 50%;
  transform: translateY(-50%);
  width: 33px;
  height: 33px;
  border: 0;
  border-radius: 10px;
  display: grid;
  place-items: center;
  color: #2563eb;
  background: #eaf1ff;
  cursor: pointer;
  transition: background 0.2s ease, color 0.2s ease, transform 0.2s ease;
}
.mic-btn:hover:not(:disabled) {
  background: #dbe7ff;
  transform: translateY(-50%) scale(1.07);
}
.mic-btn:disabled {
  opacity: 0.45;
  cursor: not-allowed;
}
.mic-btn.mic-active {
  color: #fff;
  background: #ef4444;
  animation: micPulse 1.2s ease-out infinite;
}
@keyframes micPulse {
  0% {
    box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.55);
  }
  100% {
    box-shadow: 0 0 0 12px rgba(239, 68, 68, 0);
  }
}
.chat-input-wrap.listening input {
  border-color: #fca5a5;
  background: #fff7f7;
}
.chat-input-wrap.listening .chat-input-hint {
  color: #ef4444;
  font-weight: 700;
}

/* ---- Mobile: mở rộng = gần full màn hình ---- */
@media (max-width: 600px) {
  .chat-wrapper.chat-expanded {
    right: 8px;
    bottom: 80px;
    width: calc(100vw - 16px);
    height: calc(100dvh - 96px);
  }
  .chat-expanded .chat-bubble,
  .chat-expanded .chat-bubble.user {
    max-width: 88%;
    font-size: 13px;
  }
}
</style>