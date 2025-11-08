
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cycle - Женский календарь</title>
    <link rel="manifest" href="manifest.json">
    <link rel="apple-touch-icon" href="icon-192x192.png">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Cycle">
    <style>
        /* iOS-style CSS */
        :root {
            --ios-blue: #007AFF;
            --ios-gray-1: #8E8E93;
            --ios-gray-2: #C6C6C8;
            --ios-gray-3: #F2F2F7;
            --ios-red: #FF3B30;
            --ios-green: #34C759;
            --ios-purple: #AF52DE;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background-color: var(--ios-gray-3);
            color: #000;
            max-width: 100vw;
            overflow-x: hidden;
            touch-action: pan-y;
        }
        
        /* iOS Navigation Bar */
        .nav-bar {
            background: rgba(255,255,255,0.8);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-bottom: 1px solid var(--ios-gray-2);
            padding: 44px 16px 8px 16px;
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            z-index: 100;
        }
        
        .nav-title {
            font-size: 17px;
            font-weight: 600;
            text-align: center;
        }
        
        /* Tab Bar */
        .tab-bar {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background: rgba(255,255,255,0.8);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-top: 1px solid var(--ios-gray-2);
            display: flex;
            padding: 8px 0;
            padding-bottom: env(safe-area-inset-bottom, 8px);
        }
        
        .tab-item {
            flex: 1;
            text-align: center;
            padding: 8px;
            font-size: 10px;
            color: var(--ios-gray-1);
            cursor: pointer;
        }
        
        .tab-item.active {
            color: var(--ios-blue);
        }
        
        .tab-icon {
            font-size: 24px;
            margin-bottom: 2px;
        }
        
        /* Pages Container with Swipe */
        .pages-container {
            position: relative;
            width: 100%;
            height: calc(100vh - 120px);
            margin-top: 60px;
            margin-bottom: 70px;
            overflow: hidden;
        }
        
        .page {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            transform: translateX(100%);
            transition: transform 0.3s cubic-bezier(0.25, 0.46, 0.45, 0.94);
            overflow-y: auto;
            -webkit-overflow-scrolling: touch;
        }
        
        .page.active {
            transform: translateX(0);
        }
        
        .page-content {
            padding: 16px;
            height: 100%;
            overflow-y: auto;
        }
        
        /* iOS Cards */
        .ios-card {
            background: white;
            border-radius: 10px;
            padding: 16px;
            margin-bottom: 16px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
        
        /* Status Cards */
        .status-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px;
            margin-bottom: 20px;
        }
        
        .status-card {
            background: white;
            border-radius: 12px;
            padding: 16px;
            text-align: center;
        }
        
        .status-value {
            font-size: 24px;
            font-weight: 600;
            margin: 8px 0;
        }
        
        .status-label {
            font-size: 13px;
            color: var(--ios-gray-1);
        }
        
        /* iOS Buttons */
        .ios-button {
            background: var(--ios-blue);
            color: white;
            border: none;
            border-radius: 10px;
            padding: 12px 24px;
            font-size: 17px;
            font-weight: 600;
            width: 100%;
            margin: 8px 0;
            cursor: pointer;
        }
        
        /* List Styles */
        .ios-list {
            background: white;
            border-radius: 10px;
            overflow: hidden;
        }
        
        .list-item {
            padding: 12px 16px;
            border-bottom: 1px solid var(--ios-gray-3);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .list-item:last-child {
            border-bottom: none;
        }
        
        .chevron {
            color: var(--ios-gray-2);
        }
        
        /* Calendar */
        .calendar {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 4px;
            margin-top: 16px;
        }
        
        .calendar-day {
            aspect-ratio: 1;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 8px;
            font-size: 14px;
            background: var(--ios-gray-3);
        }
        
        .calendar-day.period {
            background: var(--ios-red);
            color: white;
        }
        
        .calendar-day.fertile {
            background: var(--ios-green);
            color: white;
        }
        
        .calendar-day.today {
            border: 2px solid var(--ios-blue);
            background: white;
        }
        
        .calendar-day.other-month {
            color: var(--ios-gray-2);
            background: transparent;
        }
        
        /* Swipe Indicator */
        .swipe-indicator {
            position: fixed;
            top: 50%;
            transform: translateY(-50%);
            font-size: 24px;
            color: var(--ios-gray-2);
            z-index: 50;
            opacity: 0;
            transition: opacity 0.3s;
        }
        
        .swipe-indicator.left {
            left: 10px;
        }
        
        .swipe-indicator.right {
            right: 10px;
        }
        
        .swipe-indicator.visible {
            opacity: 1;
        }
        
        /* Page Indicators */
        .page-indicators {
            display: flex;
            justify-content: center;
            gap: 8px;
            margin-top: 16px;
        }
        
        .page-indicator {
            width: 6px;
            height: 6px;
            border-radius: 50%;
            background: var(--ios-gray-2);
            transition: background 0.3s;
        }
        
        .page-indicator.active {
            background: var(--ios-blue);
        }
        
        /* iOS Switch */
        .ios-switch {
            position: relative;
            display: inline-block;
            width: 51px;
            height: 31px;
        }
        
        .ios-switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }
        
        .slider {
            position: absolute;
            cursor: pointer;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: var(--ios-gray-2);
            transition: .4s;
            border-radius: 34px;
        }
        
        .slider:before {
            position: absolute;
            content: "";
            height: 27px;
            width: 27px;
            left: 2px;
            bottom: 2px;
            background-color: white;
            transition: .4s;
            border-radius: 50%;
        }
        
        input:checked + .slider {
            background-color: var(--ios-green);
        }
        
        input:checked + .slider:before {
            transform: translateX(20px);
        }
    </style>
</head>
<body>
    <!-- iOS Navigation -->
    <div class="nav-bar">
        <div class="nav-title" id="pageTitle">Главная</div>
    </div>

    <!-- Swipe Indicators -->
    <div class="swipe-indicator left">‹</div>
    <div class="swipe-indicator right">›</div>

    <!-- Pages Container -->
    <div class="pages-container" id="pagesContainer">
        <!-- Main Page -->
        <div class="page active" id="mainPage">
            <div class="page-content">
                <!-- Current Status -->
                <div class="status-grid">
                    <div class="status-card">
                        <div class="status-label">День цикла</div>
                        <div class="status-value" id="currentDay">1</div>
                    </div>
                    <div class="status-card">
                        <div class="status-label">До следующих</div>
                        <div class="status-value" id="daysLeft">12</div>
                    </div>
                </div>

                <!-- Quick Actions -->
                <div class="ios-card">
                    <h3 style="margin-bottom: 16px;">Быстрые действия</h3>
                    <button class="ios-button" onclick="showAddEntry()">Добавить запись</button>
                    <button class="ios-button" style="background: var(--ios-green);" onclick="showStatistics()">Статистика</button>
                </div>

                <!-- Calendar Preview -->
                <div class="ios-card">
                    <h3 style="margin-bottom: 16px;">Календарь</h3>
                    <div class="calendar" id="miniCalendar">
                        <!-- Calendar will be generated by JS -->
                    </div>
                </div>

                <!-- Recent Entries -->
                <div class="ios-card">
                    <h3 style="margin-bottom: 12px;">Последние записи</h3>
                    <div class="ios-list" id="recentEntries">
                        <!-- Entries will be added by JS -->
                    </div>
                </div>

                <!-- Page Indicators -->
                <div class="page-indicators">
                    <div class="page-indicator active"></div>
                    <div class="page-indicator"></div>
                    <div class="page-indicator"></div>
                </div>
            </div>
        </div>

        <!-- Statistics Page -->
        <div class="page" id="statsPage">
            <div class="page-content">
                <div class="ios-card">
                    <h3 style="margin-bottom: 16px;">Статистика циклов</h3>
                    <div class="status-grid">
                        <div class="status-card">
                            <div class="status-label">Средняя длина</div>
                            <div class="status-value" id="avgCycleLength">28</div>
                            <div class="status-label">дней</div>
                        </div>
                        <div class="status-card">
                            <div class="status-label">Регулярность</div>
                            <div class="status-value" id="regularity">85%</div>
                            <div class="status-label">стабильно</div>
                        </div>
                    </div>
                    
                    <div style="margin-top: 20px;">
                        <h4 style="margin-bottom: 12px;">История циклов</h4>
                        <div class="ios-list" id="cycleHistory">
                            <!-- Cycle history will be added by JS -->
                        </div>
                    </div>
                </div>

                <!-- Page Indicators -->
                <div class="page-indicators">
                    <div class="page-indicator"></div>
                    <div class="page-indicator active"></div>
                    <div class="page-indicator"></div>
                </div>
            </div>
        </div>

        <!-- Settings Page -->
        <div class="page" id="settingsPage">
            <div class="page-content">
                <div class="ios-card">
                    <h3 style="margin-bottom: 16px;">Настройки</h3>
                    
                    <div class="list-item">
                        <div>Уведомления</div>
                        <label class="ios-switch">
                            <input type="checkbox" id="notificationsToggle" checked>
                            <span class="slider"></span>
                        </label>
                    </div>
                    
                    <div class="list-item">
                        <div>Длина цикла</div>
                        <div style="color: var(--ios-gray-1);" id="cycleLengthValue">28 дней</div>
                    </div>
                    
                    <div class="list-item">
                        <div>Длина периода</div>
                        <div style="color: var(--ios-gray-1);" id="periodLengthValue">5 дней</div>
                    </div>
                    
                    <button class="ios-button" style="background: var(--ios-red); margin-top: 20px;" onclick="clearAllData()">
                        Очистить все данные
                    </button>
                </div>

                <!-- Page Indicators -->
                <div class="page-indicators">
                    <div class="page-indicator"></div>
                    <div class="page-indicator"></div>
                    <div class="page-indicator active"></div>
                </div>
            </div>
        </div>
    </div>

    <!-- iOS Tab Bar -->
    <div class="tab-bar">
        <div class="tab-item active" onclick="showPage('mainPage', 'Главная')">
            <div class="tab-icon">📅</div>
            <div>Главная</div>
        </div>
        <div class="tab-item" onclick="showPage('statsPage', 'Статистика')">
            <div class="tab-icon">📊</div>
            <div>Статистика</div>
        </div>
        <div class="tab-item" onclick="showPage('settingsPage', 'Настройки')">
            <div class="tab-icon">⚙️</div>
            <div>Настройки</div>
        </div>
    </div>

    <script>
        // Основной класс приложения
        class WomensCalendar {
            constructor() {
                this.cycles = JSON.parse(localStorage.getItem('cycles')) || [];
                this.settings = JSON.parse(localStorage.getItem('settings')) || {
                    cycleLength: 28,
                    periodLength: 5,
                    notifications: true
                };
                this.currentPage = 'mainPage';
                this.pages = ['mainPage', 'statsPage', 'settingsPage'];
                this.init();
            }

            init() {
                this.updateUI();
                this.generateMiniCalendar();
                this.setupSwipe();
                this.updateCycleHistory();
                this.loadSettings();
            }

            addCycle(startDate, symptoms = [], notes = '') {
                const cycle = {
                    startDate: new Date(startDate),
                    symptoms,
                    notes,
                    createdAt: new Date()
                };
                
                this.cycles.push(cycle);
                this.saveToStorage();
                this.updateUI();
                this.updateCycleHistory();
            }

            predictNextCycle() {
                if (this.cycles.length < 1) return null;
                
                const lastCycle = this.cycles[this.cycles.length - 1];
                const avgLength = this.calculateAverageCycleLength();
                
                return new Date(
                    lastCycle.startDate.getTime() + (avgLength * 24 * 60 * 60 * 1000)
                );
            }

            calculateAverageCycleLength() {
                if (this.cycles.length < 2) return this.settings.cycleLength;
                
                let totalDays = 0;
                for (let i = 1; i < this.cycles.length; i++) {
                    const diff = this.cycles[i].startDate - this.cycles[i-1].startDate;
                    totalDays += Math.floor(diff / (1000 * 60 * 60 * 24));
                }
                
                return Math.floor(totalDays / (this.cycles.length - 1));
            }

            getCurrentCycleDay() {
                if (this.cycles.length === 0) return 1;
                
                const lastCycle = this.cycles[this.cycles.length - 1];
                const today = new Date();
                const diffTime = today - lastCycle.startDate;
                const diffDays = Math.floor(diffTime / (1000 * 60 * 60 * 24));
                
                return diffDays + 1;
            }

            getDaysUntilNext() {
                const nextCycle = this.predictNextCycle();
                if (!nextCycle) return '-';
                
                const today = new Date();
                const diffTime = nextCycle - today;
                const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
                
                return Math.max(0, diffDays);
            }

            saveToStorage() {
                localStorage.setItem('cycles', JSON.stringify(this.cycles));
                localStorage.setItem('settings', JSON.stringify(this.settings));
            }

            updateUI() {
                document.getElementById('currentDay').textContent = this.getCurrentCycleDay();
                document.getElementById('daysLeft').textContent = this.getDaysUntilNext();
                document.getElementById('avgCycleLength').textContent = this.calculateAverageCycleLength();
                this.updateRecentEntries();
            }

            updateRecentEntries() {
                const container = document.getElementById('recentEntries');
                if (this.cycles.length === 0) {
                    container.innerHTML = '<div class="list-item">Нет записей</div>';
                    return;
                }

                const recent = this.cycles.slice(-3).reverse();
                container.innerHTML = recent.map(cycle => `
                    <div class="list-item">
                        <div>
                            <strong>${cycle.startDate.toLocaleDateString('ru-RU')}</strong>
                            <div style="font-size: 13px; color: var(--ios-gray-1);">
                                ${cycle.symptoms.length} симптомов
                            </div>
                        </div>
                        <div class="chevron">›</div>
                    </div>
                `).join('');
            }

            generateMiniCalendar() {
                const calendar = document.getElementById('miniCalendar');
                const today = new Date();
                const firstDay = new Date(today.getFullYear(), today.getMonth(), 1);
                
                // Очищаем календарь
                calendar.innerHTML = '';
                
                // Добавляем заголовки дней
                const days = ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'];
                days.forEach(day => {
                    const dayElement = document.createElement('div');
                    dayElement.style.textAlign = 'center';
                    dayElement.style.fontSize = '12px';
                    dayElement.style.color = 'var(--ios-gray-1)';
                    dayElement.textContent = day;
                    calendar.appendChild(dayElement);
                });
                
                // Добавляем дни
                const startDay = firstDay.getDay() === 0 ? 6 : firstDay.getDay() - 1; // Корректировка для понедельника
                
                for (let i = 0; i < 42; i++) {
                    const dayElement = document.createElement('div');
                    dayElement.className = 'calendar-day';
                    
                    const currentDate = new Date(firstDay);
                    currentDate.setDate(firstDay.getDate() + i - startDay);
                    
                    const isToday = currentDate.toDateString() === today.toDateString();
                    const isCurrentMonth = currentDate.getMonth() === today.getMonth();
                    
                    if (!isCurrentMonth) {
                        dayElement.classList.add('other-month');
                    }
                    
                    if (isToday) {
                        dayElement.classList.add('today');
                    }
                    
                    // Простая логика для отображения периода (для демонстрации)
                    if (this.cycles.length > 0) {
                        const lastCycle = this.cycles[this.cycles.length - 1];
                        const cycleStart = new Date(lastCycle.startDate);
                        const periodEnd = new Date(cycleStart);
                        periodEnd.setDate(cycleStart.getDate() + this.settings.periodLength - 1);
                        
                        if (currentDate >= cycleStart && currentDate <= periodEnd) {
                            dayElement.classList.add('period');
                        }
                    }
                    
                    dayElement.textContent = currentDate.getDate();
                    calendar.appendChild(dayElement);
                }
            }

            updateCycleHistory() {
                const container = document.getElementById('cycleHistory');
                if (this.cycles.length === 0) {
                    container.innerHTML = '<div class="list-item">Нет данных о циклах</div>';
                    return;
                }

                const history = this.cycles.slice(-5).reverse();
                container.innerHTML = history.map((cycle, index) => `
                    <div class="list-item">
                        <div>
                            <strong>Цикл ${this.cycles.length - index}</strong>
                            <div style="font-size: 13px; color: var(--ios-gray-1);">
                                ${cycle.startDate.toLocaleDateString('ru-RU')}
                            </div>
                        </div>
                        <div style="color: var(--ios-gray-1);">
                            ${this.calculateCycleLength(cycle)} дн.
                        </div>
                    </div>
                `).join('');
            }

            calculateCycleLength(cycle) {
                // Упрощенная логика
                return this.settings.cycleLength;
            }

            loadSettings() {
                document.getElementById('cycleLengthValue').textContent = `${this.settings.cycleLength} дней`;
                document.getElementById('periodLengthValue').textContent = `${this.settings.periodLength} дней`;
                document.getElementById('notificationsToggle').checked = this.settings.notifications;
            }

            setupSwipe() {
                const container = document.getElementById('pagesContainer');
                let startX = 0;
                let currentX = 0;
                let isSwiping = false;

                container.addEventListener('touchstart', (e) => {
                    startX = e.touches[0].clientX;
                    isSwiping = true;
                });

                container.addEventListener('touchmove', (e) => {
                    if (!isSwiping) return;
                    
                    currentX = e.touches[0].clientX;
                    const diff = currentX - startX;
                    
                    this.showSwipeIndicators(diff);
                });

                container.addEventListener('touchend', (e) => {
                    if (!isSwiping) return;
                    
                    const diff = currentX - startX;
                    const swipeThreshold = 50;
                    
                    if (Math.abs(diff) > swipeThreshold) {
                        if (diff > 0) {
                            this.swipeToPreviousPage();
                        } else {
                            this.swipeToNextPage();
                        }
                    }
                    
                    this.hideSwipeIndicators();
                    isSwiping = false;
                });

                // Поддержка мыши для десктопа
                container.addEventListener('mousedown', (e) => {
                    startX = e.clientX;
                    isSwiping = true;
                });

                container.addEventListener('mousemove', (e) => {
                    if (!isSwiping) return;
                    currentX = e.clientX;
                });

                container.addEventListener('mouseup', (e) => {
                    if (!isSwiping) return;
                    
                    const diff = currentX - startX;
                    const swipeThreshold = 50;
                    
                    if (Math.abs(diff) > swipeThreshold) {
                        if (diff > 0) {
                            this.swipeToPreviousPage();
                        } else {
                            this.swipeToNextPage();
                        }
                    }
                    
                    this.hideSwipeIndicators();
                    isSwiping = false;
                });

                container.addEventListener('mouseleave', () => {
                    if (isSwiping) {
                        this.hideSwipeIndicators();
                        isSwiping = false;
                    }
                });
            }

            showSwipeIndicators(diff) {
                const leftIndicator = document.querySelector('.swipe-indicator.left');
                const rightIndicator = document.querySelector('.swipe-indicator.right');
                
                if (diff > 30) {
                    leftIndicator.classList.add('visible');
                    rightIndicator.classList.remove('visible');
                } else if (diff < -30) {
                    rightIndicator.classList.add('visible');
                    leftIndicator.classList.remove('visible');
                } else {
                    this.hideSwipeIndicators();
                }
            }

            hideSwipeIndicators() {
                document.querySelectorAll('.swipe-indicator').forEach(indicator => {
                    indicator.classList.remove('visible');
                });
            }

            swipeToNextPage() {
                const currentIndex = this.pages.indexOf(this.currentPage);
                if (currentIndex < this.pages.length - 1) {
                    const nextPage = this.pages[currentIndex + 1];
                    this.showPage(nextPage, this.getPageTitle(nextPage));
                }
            }

            swipeToPreviousPage() {
                const currentIndex = this.pages.indexOf(this.currentPage);
                if (currentIndex > 0) {
                    const prevPage = this.pages[currentIndex - 1];
                    this.showPage(prevPage, this.getPageTitle(prevPage));
                }
            }

            getPageTitle(pageId) {
                const titles = {
                    'mainPage': 'Главная',
                    'statsPage': 'Статистика',
                    'settingsPage': 'Настройки'
                };
                return titles[pageId] || 'Главная';
            }

            showPage(pageId, title) {
                this.currentPage = pageId;
                
                document.querySelectorAll('.page').forEach(page => {
                    page.classList.remove('active');
                });
                
                document.getElementById(pageId).classList.add('active');
                document.getElementById('pageTitle').textContent = title;
                
                document.querySelectorAll('.tab-item').forEach((tab, index) => {
                    tab.classList.toggle('active', this.pages[index] === pageId);
                });
                
                this.updatePageIndicators();
            }

            updatePageIndicators() {
                const indicators = document.querySelectorAll('.page-indicator');
                const currentIndex = this.pages.indexOf(this.currentPage);
                
                indicators.forEach((indicator, index) => {
                    indicator.classList.toggle('active', index === currentIndex);
                });
            }
        }

        // Глобальные функции
        function showPage(pageId, title) {
            app.showPage(pageId, title);
        }

        function showAddEntry() {
            const date = new Date().toISOString().split('T')[0];
            const selectedDate = prompt('Введите дату начала цикла (ГГГГ-ММ-ДД):', date);
            
            if (selectedDate) {
                app.addCycle(selectedDate);
                if (navigator.vibrate) {
                    navigator.vibrate(50);
                }
                alert('Запись добавлена!');
            }
        }

        function showStatistics() {
            showPage('statsPage', 'Статистика');
        }

        function clearAllData() {
            if (confirm('Вы уверены, что хотите удалить все данные? Это действие нельзя отменить.')) {
                localStorage.clear();
                app = new WomensCalendar();
                if (navigator.vibrate) {
                    navigator.vibrate(200);
                }
                alert('Все данные удалены');
            }
        }

        // Initialize app
        let app = new WomensCalendar();

        // PWA Installation
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('sw.js');
        }

        // iOS-style touch interactions
        document.addEventListener('touchstart', function() {}, {passive: true});
    </script>
</body>
</html>
{
  "name": "Cycle - Женский календарь",
  "short_name": "Cycle",
  "start_url": "./index.html",
  "display": "standalone",
  "background_color": "#F2F2F7",
  "theme_color": "#007AFF",
  "icons": [
    {
      "src": "icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    }
  ]
}
self.addEventListener('install', (e) => {
  console.log('Service Worker installed');
  self.skipWaiting();
});

self.addEventListener('activate', (e) => {
  console.log('Service Worker activated');
});

self.addEventListener('fetch', (e) => {
  e.respondWith(fetch(e.request));
});
