<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Chia sẻ vị trí của bạn</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css"/>
  <style>
    .hidden { display: none; }
    .map-container { height: 400px; border-radius: 1rem; overflow: hidden; box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1); transition: all 0.3s ease; }
    .map-container:hover { box-shadow: 0 20px 25px -5px rgba(0,0,0,0.1); }
    .copy-btn { transition: all 0.2s ease; }
    .copy-btn:hover { transform: scale(1.05); }
    .copy-btn:active { transform: scale(0.95); }
  </style>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center p-4">
  <div class="bg-white max-w-2xl w-full rounded-2xl shadow-xl overflow-hidden">
    <div class="p-6 bg-blue-600 text-white">
      <h1 class="text-2xl font-bold flex items-center">
        <i class="fas fa-location-arrow mr-3"></i>
        Chia sẻ vị trí của tôi
      </h1>
      <p class="mt-2 opacity-90">Nhấn nút bên dưới để tạo liên kết chia sẻ vị trí hiện tại của bạn</p>
    </div>
    <div class="p-6">
      <div id="share-section" class="hidden">
        <p class="mb-4 text-gray-700">Liên kết vị trí của bạn đã sẵn sàng! Chia sẻ cho bất cứ ai:</p>
        <div class="flex mb-4">
          <input id="shareLink" type="text" readonly class="flex-1 border border-gray-300 rounded-l-lg px-4 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"/>
          <button id="copyBtn" class="copy-btn bg-blue-600 text-white px-4 py-2 rounded-r-lg hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500">
            Sao chép
          </button>
        </div>
        <div class="map-container mb-4">
          <iframe id="mapFrame" class="w-full h-full border-0" allowfullscreen></iframe>
        </div>
        <button id="messengerShareBtn" class="flex w-full mb-4 items-center justify-center gap-2 px-4 py-2 bg-blue-800 text-white rounded-lg hover:bg-blue-900 transition-colors">
          <i class="fab fa-facebook-messenger"></i> Chia sẻ qua Messenger
        </button>
        <div class="flex items-center text-sm text-gray-500">
          <i class="fas fa-info-circle mr-2"></i>
          Liên kết sẽ mở Google Maps hiển thị vị trí chính xác của bạn
        </div>
      </div>
      <button id="getLocationBtn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 rounded-lg flex items-center justify-center transition duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500">
        <i class="fas fa-map-marker-alt mr-2"></i>
        Tạo liên kết vị trí của tôi
      </button>
      <div class="mt-6 pt-6 border-t border-gray-200 text-center text-sm text-gray-500">
        Không có dữ liệu vị trí nào được lưu trữ. Quyền riêng tư của bạn được bảo vệ.
      </div>
    </div>
  </div>

  <script>
    document.addEventListener('DOMContentLoaded', () => {
      const getLocationBtn = document.getElementById('getLocationBtn');
      const shareSection = document.getElementById('share-section');
      const shareLink = document.getElementById('shareLink');
      const copyBtn = document.getElementById('copyBtn');
      const mapFrame = document.getElementById('mapFrame');
      const messengerBtn = document.getElementById('messengerShareBtn');

      function showOnMap(lat, lng) {
        shareSection.classList.remove('hidden');
        shareLink.value = window.location.origin + window.location.pathname + `?lat=${lat}&lng=${lng}`;
        mapFrame.src = `https://maps.google.com/maps?q=${lat},${lng}&z=15&output=embed`;
      }

      function shareMessenger() {
        const url = encodeURIComponent(shareLink.value);
        window.open(`https://m.me/share?u=${url}`, '_blank');
      }

      getLocationBtn.addEventListener('click', () => {
        getLocationBtn.disabled = true;
        getLocationBtn.innerHTML = '<i class="fas fa-spinner fa-spin mr-2"></i> Đang lấy vị trí...';
        navigator.geolocation.getCurrentPosition(
          pos => {
            showOnMap(pos.coords.latitude, pos.coords.longitude);
            getLocationBtn.disabled = false;
            getLocationBtn.innerHTML = '<i class="fas fa-map-marker-alt mr-2"></i> Tạo liên kết vị trí của tôi';
          },
          err => {
            alert('Lỗi xác định vị trí: ' + err.message);
            getLocationBtn.disabled = false;
            getLocationBtn.innerHTML = '<i class="fas fa-map-marker-alt mr-2"></i> Tạo liên kết vị trí của tôi';
          }
        );
      });

      copyBtn.addEventListener('click', () => {
        shareLink.select();
        document.execCommand('copy');
        copyBtn.innerHTML = '<i class="fas fa-check mr-1"></i> Đã sao chép!';
        setTimeout(() => {
          copyBtn.innerHTML = 'Sao chép';
        }, 2000);
      });

      messengerBtn.addEventListener('click', shareMessenger);

      const params = new URLSearchParams(window.location.search);
      if (params.get('lat') && params.get('lng')) {
        showOnMap(params.get('lat'), params.get('lng'));
        getLocationBtn.classList.add('hidden');
      }
    });
  </script>
</body>
</html>
