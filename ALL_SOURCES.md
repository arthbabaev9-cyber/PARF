# 📁 ПОЛНЫЙ ТЕКСТ ВСЕХ ФАЙЛОВ ПРОЕКТА
# =====================================
# Скопируйте каждый файл в соответствующую папку на вашем компьютере.
# Структура проекта:
#
# aromas-app/
# ├── index.html
# ├── package.json
# ├── vite.config.js
# ├── tsconfig.json
# ├── capacitor.config.json
# ├── src/
# │   ├── main.tsx
# │   ├── App.tsx
# │   └── index.css
# └── public/
#     ├── manifest.json
#     └── icon.svg


# ═══════════════════════════════════════════
# ФАЙЛ: package.json
# ═══════════════════════════════════════════

{
  "name": "aromas-kassa",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "typecheck": "tsc --noEmit"
  },
  "dependencies": {
    "@capacitor/android": "^8.6.2",
    "@capacitor/cli": "^8.6.2",
    "@capacitor/core": "^8.6.2",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "xlsx": "^0.18.5"
  },
  "devDependencies": {
    "@tailwindcss/vite": "^4.1.7",
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@vitejs/plugin-react": "^4.3.4",
    "tailwindcss": "^4.1.7",
    "typescript": "^5.7.0",
    "vite": "^6.3.5"
  }
}


# ═══════════════════════════════════════════
# ФАЙЛ: vite.config.js
# ═══════════════════════════════════════════

import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
  server: {
    host: "0.0.0.0",
    port: 3000,
    strictPort: true,
    hmr: {
      port: 3000,
    },
  },
});


# ═══════════════════════════════════════════
# ФАЙЛ: tsconfig.json
# ═══════════════════════════════════════════

{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "jsx": "react-jsx",
    "moduleResolution": "bundler",
    "strict": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "isolatedModules": true,
    "noEmit": true,
    "allowImportingTsExtensions": true
  },
  "include": ["src"]
}


# ═══════════════════════════════════════════
# ФАЙЛ: capacitor.config.json
# ═══════════════════════════════════════════

{
  "appId": "com.aromas.kassa",
  "appName": "Ароматы Касса",
  "webDir": "dist",
  "server": {
    "androidScheme": "https"
  },
  "android": {
    "allowMixedContent": true,
    "backgroundColor": "#111827"
  }
}


# ═══════════════════════════════════════════
# ФАЙЛ: index.html
# ═══════════════════════════════════════════

<!doctype html>
<html lang="ru">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <meta name="theme-color" content="#7c3aed" />
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
    <meta name="mobile-web-app-capable" content="yes" />
    <title>Ароматы - Касса</title>
    <link rel="manifest" href="/manifest.json" />
    <link rel="icon" href="/icon.svg" />
    <link rel="apple-touch-icon" href="/icon.svg" />
    <style>
      html, body {
        margin: 0;
        padding: 0;
        width: 100%;
        height: 100%;
        background-color: #111827;
        color: #ffffff;
      }
    </style>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>


# ═══════════════════════════════════════════
# ФАЙЛ: src/main.tsx
# ═══════════════════════════════════════════

import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./App.tsx";

ReactDOM.createRoot(document.getElementById("root")!).render(<App />);


# ═══════════════════════════════════════════
# ФАЙЛ: src/index.css
# ═══════════════════════════════════════════

@import "tailwindcss";


# ═══════════════════════════════════════════
# ФАЙЛ: src/App.tsx
# ═══════════════════════════════════════════

import { useState, useCallback, useEffect } from 'react';
import * as XLSX from 'xlsx';

interface Aroma {
  name: string;
  prices: { [key: number]: number };
}

interface CartItem {
  aroma: Aroma;
  volume: number;
  quantity: number;
  price: number;
}

const VOLUMES = [5, 10, 15, 20, 30, 50];

const DEMO_DATA: Aroma[] = [
  { name: 'Роза Дамасская', prices: { 5: 150, 10: 280, 15: 400, 20: 520, 30: 750, 50: 1200 } },
  { name: 'Ваниль Мадагаскар', prices: { 5: 180, 10: 340, 15: 480, 20: 620, 30: 900, 50: 1450 } },
  { name: 'Лаванда Прованс', prices: { 5: 120, 10: 220, 15: 320, 20: 420, 30: 600, 50: 950 } },
  { name: 'Жасмин Арабский', prices: { 5: 200, 10: 380, 15: 540, 20: 700, 30: 1000, 50: 1600 } },
  { name: 'Сандал Белый', prices: { 5: 250, 10: 470, 15: 670, 20: 870, 30: 1250, 50: 2000 } },
  { name: 'Бергамот Итальянский', prices: { 5: 130, 10: 240, 15: 350, 20: 450, 30: 650, 50: 1050 } },
  { name: 'Пачули Индийский', prices: { 5: 160, 10: 300, 15: 430, 20: 560, 30: 800, 50: 1300 } },
  { name: 'Мускус Белый', prices: { 5: 140, 10: 260, 15: 370, 20: 480, 30: 700, 50: 1100 } },
];

function App() {
  const [aromas, setAromas] = useState<Aroma[]>(DEMO_DATA);
  const [cart, setCart] = useState<CartItem[]>([]);
  const [searchTerm, setSearchTerm] = useState('');
  const [showCart, setShowCart] = useState(false);
  const [showHelp, setShowHelp] = useState(false);
  const [selectedVolumes, setSelectedVolumes] = useState<{ [key: string]: number }>({});
  const [fileName, setFileName] = useState<string>('');

  useEffect(() => {
    const seen = localStorage.getItem('helpSeen');
    if (!seen) {
      setShowHelp(true);
      localStorage.setItem('helpSeen', 'true');
    }
  }, []);

  const handleFileUpload = useCallback((e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0];
    if (!file) return;

    setFileName(file.name);
    const reader = new FileReader();
    reader.onload = (event) => {
      const data = event.target?.result;
      if (!data) return;

      const workbook = XLSX.read(data, { type: 'binary' });
      const sheetName = workbook.SheetNames[0];
      const worksheet = workbook.Sheets[sheetName];
      const jsonData = XLSX.utils.sheet_to_json<string[]>(worksheet, { header: 1 });

      if (jsonData.length < 2) return;

      const headers = jsonData[0] as string[];
      const volumeColumns: { [key: number]: number } = {};
      
      headers.forEach((header, index) => {
        const headerStr = String(header).toLowerCase().replace(/\s/g, '');
        VOLUMES.forEach(vol => {
          if (headerStr.includes(String(vol))) {
            volumeColumns[vol] = index;
          }
        });
      });

      const parsedAromas: Aroma[] = [];
      for (let i = 1; i < jsonData.length; i++) {
        const row = jsonData[i] as string[];
        if (!row || !row[0]) continue;

        const name = String(row[0]).trim();
        if (!name) continue;

        const prices: { [key: number]: number } = {};
        VOLUMES.forEach(vol => {
          const colIdx = volumeColumns[vol];
          if (colIdx !== undefined && row[colIdx]) {
            prices[vol] = Number(row[colIdx]) || 0;
          } else {
            prices[vol] = 0;
          }
        });

        parsedAromas.push({ name, prices });
      }

      if (parsedAromas.length > 0) {
        setAromas(parsedAromas);
      }
    };
    reader.readAsBinaryString(file);
  }, []);

  const addToCart = (aroma: Aroma, volume: number) => {
    const price = aroma.prices[volume] || 0;
    if (price === 0) return;

    const existingIndex = cart.findIndex(
      item => item.aroma.name === aroma.name && item.volume === volume
    );

    if (existingIndex >= 0) {
      const newCart = [...cart];
      newCart[existingIndex] = {
        ...newCart[existingIndex],
        quantity: newCart[existingIndex].quantity + 1,
      };
      setCart(newCart);
    } else {
      setCart([...cart, { aroma, volume, quantity: 1, price }]);
    }
  };

  const removeFromCart = (index: number) => {
    setCart(cart.filter((_, i) => i !== index));
  };

  const updateQuantity = (index: number, delta: number) => {
    const newCart = [...cart];
    newCart[index] = {
      ...newCart[index],
      quantity: Math.max(1, newCart[index].quantity + delta),
    };
    setCart(newCart);
  };

  const clearCart = () => {
    setCart([]);
  };

  const getTotal = () => {
    return cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
  };

  const getTotalItems = () => {
    return cart.reduce((sum, item) => sum + item.quantity, 0);
  };

  const filteredAromas = aromas.filter(aroma =>
    aroma.name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  const getVolumeForAroma = (aromaName: string) => {
    return selectedVolumes[aromaName] || 10;
  };

  return (
    <div className="min-h-screen bg-gray-900 text-white">
      {/* Header */}
      <header className="bg-gray-800 border-b border-gray-700 sticky top-0 z-50">
        <div className="max-w-4xl mx-auto px-4 py-3 flex items-center justify-between">
          <div className="flex items-center gap-3">
            <span className="text-2xl">🧴</span>
            <h1 className="text-xl font-bold bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">
              Ароматы
            </h1>
          </div>
          <div className="flex items-center gap-2">
            <button
              onClick={() => setShowHelp(true)}
              className="w-10 h-10 rounded-lg bg-gray-700 hover:bg-gray-600 flex items-center justify-center transition-colors"
              title="Инструкция"
            >
              <span className="text-lg">❓</span>
            </button>
            <button
              onClick={() => setShowCart(!showCart)}
              className="relative bg-purple-600 hover:bg-purple-700 px-4 py-2 rounded-lg flex items-center gap-2 transition-colors"
            >
              <span className="text-lg">🛒</span>
              <span className="font-medium hidden sm:inline">Корзина</span>
              {cart.length > 0 && (
                <span className="absolute -top-2 -right-2 bg-pink-500 text-white text-xs w-5 h-5 rounded-full flex items-center justify-center font-bold">
                  {getTotalItems()}
                </span>
              )}
            </button>
          </div>
        </div>
      </header>

      <main className="max-w-4xl mx-auto px-4 py-4">
        {/* File Upload */}
        <div className="mb-4 bg-gray-800 rounded-xl p-4 border border-gray-700">
          <div className="flex items-center gap-3 mb-2">
            <span className="text-lg">📊</span>
            <span className="font-medium text-gray-300">Загрузить Excel таблицу</span>
            {fileName && (
              <span className="text-sm text-green-400 ml-2">✓ {fileName}</span>
            )}
          </div>
          <label className="block cursor-pointer">
            <div className="border-2 border-dashed border-gray-600 rounded-lg p-4 text-center hover:border-purple-500 transition-colors">
              <p className="text-gray-400 text-sm">
                Нажмите для выбора файла (.xlsx, .xls)
              </p>
              <p className="text-gray-500 text-xs mt-1">
                Формат: Название | 5ml | 10ml | 15ml | 20ml | 30ml | 50ml
              </p>
            </div>
            <input
              type="file"
              accept=".xlsx,.xls"
              onChange={handleFileUpload}
              className="hidden"
            />
          </label>
        </div>

        {/* Search */}
        <div className="mb-4">
          <div className="relative">
            <span className="absolute left-3 top-1/2 -translate-y-1/2 text-gray-400">🔍</span>
            <input
              type="text"
              placeholder="Поиск аромата..."
              value={searchTerm}
              onChange={(e) => setSearchTerm(e.target.value)}
              className="w-full bg-gray-800 border border-gray-700 rounded-xl pl-10 pr-4 py-3 text-white placeholder-gray-500 focus:outline-none focus:border-purple-500 transition-colors"
            />
          </div>
        </div>

        {/* Help Modal */}
        {showHelp && (
          <div className="fixed inset-0 z-50 bg-black/70 flex items-center justify-center p-4">
            <div className="bg-gray-800 w-full max-w-md rounded-2xl border border-gray-700 max-h-[85vh] overflow-y-auto">
              <div className="flex items-center justify-between p-4 border-b border-gray-700 sticky top-0 bg-gray-800">
                <h2 className="text-lg font-bold">📖 Как пользоваться</h2>
                <button
                  onClick={() => setShowHelp(false)}
                  className="text-gray-400 hover:text-white text-2xl"
                >
                  ×
                </button>
              </div>
              
              <div className="p-4 space-y-4 text-sm">
                <div className="bg-purple-900/30 border border-purple-700 rounded-xl p-3">
                  <p className="font-bold text-purple-300 mb-1">📱 Установка на телефон</p>
                  <p className="text-gray-300">
                    Откройте эту страницу в Chrome на телефоне → нажмите <b>⋮</b> (три точки) → <b>«Добавить на главный экран»</b>. 
                    Приложение будет работать как обычное!
                  </p>
                </div>

                <div className="space-y-3">
                  <div className="flex gap-3">
                    <span className="text-xl">1️⃣</span>
                    <div>
                      <p className="font-medium text-white">Загрузите таблицу Excel</p>
                      <p className="text-gray-400 mt-1">
                        Создайте Excel-файл с колонками:<br/>
                        <code className="bg-gray-700 px-1 rounded text-xs">Название | 5 | 10 | 15 | 20 | 30 | 50</code>
                      </p>
                    </div>
                  </div>

                  <div className="flex gap-3">
                    <span className="text-xl">2️⃣</span>
                    <div>
                      <p className="font-medium text-white">Выберите объём</p>
                      <p className="text-gray-400 mt-1">
                        Для каждого аромата нажмите нужный объём (5, 10, 15, 20, 30 или 50 мл).
                      </p>
                    </div>
                  </div>

                  <div className="flex gap-3">
                    <span className="text-xl">3️⃣</span>
                    <div>
                      <p className="font-medium text-white">Добавьте в корзину</p>
                      <p className="text-gray-400 mt-1">
                        Нажмите «В корзину» — товар добавится.
                      </p>
                    </div>
                  </div>

                  <div className="flex gap-3">
                    <span className="text-xl">4️⃣</span>
                    <div>
                      <p className="font-medium text-white">Проверьте корзину</p>
                      <p className="text-gray-400 mt-1">
                        Нажмите 🛒 — увидите все товары и итоговую сумму.
                      </p>
                    </div>
                  </div>
                </div>

                <div className="bg-gray-700/50 rounded-xl p-3 border border-gray-600">
                  <p className="font-medium text-white mb-2">📋 Пример Excel-файла:</p>
                  <div className="overflow-x-auto">
                    <table className="text-xs w-full">
                      <thead>
                        <tr className="text-purple-300">
                          <th className="text-left p-1">Название</th>
                          <th className="p-1">5</th>
                          <th className="p-1">10</th>
                          <th className="p-1">15</th>
                          <th className="p-1">20</th>
                          <th className="p-1">30</th>
                          <th className="p-1">50</th>
                        </tr>
                      </thead>
                      <tbody className="text-gray-300">
                        <tr>
                          <td className="p-1">Роза</td>
                          <td className="p-1 text-center">150</td>
                          <td className="p-1 text-center">280</td>
                          <td className="p-1 text-center">400</td>
                          <td className="p-1 text-center">520</td>
                          <td className="p-1 text-center">750</td>
                          <td className="p-1 text-center">1200</td>
                        </tr>
                        <tr>
                          <td className="p-1">Ваниль</td>
                          <td className="p-1 text-center">180</td>
                          <td className="p-1 text-center">340</td>
                          <td className="p-1 text-center">480</td>
                          <td className="p-1 text-center">620</td>
                          <td className="p-1 text-center">900</td>
                          <td className="p-1 text-center">1450</td>
                        </tr>
                      </tbody>
                    </table>
                  </div>
                </div>
              </div>

              <div className="p-4 border-t border-gray-700">
                <button
                  onClick={() => setShowHelp(false)}
                  className="w-full bg-purple-600 hover:bg-purple-700 py-3 rounded-xl font-medium transition-colors"
                >
                  Понятно! ✓
                </button>
              </div>
            </div>
          </div>
        )}

        {/* Cart Panel */}
        {showCart && (
          <div className="fixed inset-0 z-50 bg-black/60 flex items-end sm:items-center justify-center">
            <div className="bg-gray-800 w-full max-w-lg max-h-[85vh] rounded-t-2xl sm:rounded-2xl border border-gray-700 flex flex-col">
              <div className="flex items-center justify-between p-4 border-b border-gray-700">
                <h2 className="text-lg font-bold flex items-center gap-2">
                  <span>🛒</span> Корзина
                </h2>
                <button
                  onClick={() => setShowCart(false)}
                  className="text-gray-400 hover:text-white text-2xl"
                >
                  ×
                </button>
              </div>
              
              <div className="flex-1 overflow-y-auto p-4">
                {cart.length === 0 ? (
                  <div className="text-center text-gray-500 py-8">
                    <p className="text-4xl mb-2">🛒</p>
                    <p>Корзина пуста</p>
                  </div>
                ) : (
                  <div className="space-y-3">
                    {cart.map((item, index) => (
                      <div key={index} className="bg-gray-700/50 rounded-xl p-3 border border-gray-600">
                        <div className="flex justify-between items-start mb-2">
                          <div>
                            <p className="font-medium text-white">{item.aroma.name}</p>
                            <p className="text-sm text-purple-400">{item.volume} ml</p>
                          </div>
                          <button
                            onClick={() => removeFromCart(index)}
                            className="text-red-400 hover:text-red-300 text-lg"
                          >
                            ✕
                          </button>
                        </div>
                        <div className="flex items-center justify-between">
                          <div className="flex items-center gap-2">
                            <button
                              onClick={() => updateQuantity(index, -1)}
                              className="w-8 h-8 rounded-lg bg-gray-600 hover:bg-gray-500 flex items-center justify-center font-bold transition-colors"
                            >
                              −
                            </button>
                            <span className="w-8 text-center font-medium">{item.quantity}</span>
                            <button
                              onClick={() => updateQuantity(index, 1)}
                              className="w-8 h-8 rounded-lg bg-gray-600 hover:bg-gray-500 flex items-center justify-center font-bold transition-colors"
                            >
                              +
                            </button>
                          </div>
                          <p className="font-bold text-green-400">
                            {item.price * item.quantity} ₽
                          </p>
                        </div>
                      </div>
                    ))}
                  </div>
                )}
              </div>

              {cart.length > 0 && (
                <div className="border-t border-gray-700 p-4">
                  <div className="flex justify-between items-center mb-3">
                    <span className="text-gray-400">Итого ({getTotalItems()} шт.):</span>
                    <span className="text-2xl font-bold text-green-400">{getTotal()} ₽</span>
                  </div>
                  <div className="flex gap-2">
                    <button
                      onClick={clearCart}
                      className="flex-1 bg-red-600/20 text-red-400 hover:bg-red-600/30 py-3 rounded-xl font-medium transition-colors"
                    >
                      Очистить
                    </button>
                    <button
                      onClick={() => {
                        let receipt = '🧾 ЧЕК\n';
                        receipt += '════════════════\n';
                        cart.forEach(item => {
                          receipt += `${item.aroma.name} (${item.volume}ml)\n`;
                          receipt += `  ${item.quantity} × ${item.price} = ${item.price * item.quantity} ₽\n`;
                        });
                        receipt += '════════════════\n';
                        receipt += `ИТОГО: ${getTotal()} ₽\n`;
                        receipt += `Товаров: ${getTotalItems()} шт.`;
                        alert(receipt);
                      }}
                      className="flex-1 bg-green-600 hover:bg-green-700 py-3 rounded-xl font-medium transition-colors"
                    >
                      Оформить
                    </button>
                  </div>
                </div>
              )}
            </div>
          </div>
        )}

        {/* Aroma List */}
        <div className="space-y-3 pb-20">
          {filteredAromas.length === 0 ? (
            <div className="text-center text-gray-500 py-12">
              <p className="text-4xl mb-2">🔍</p>
              <p>Ничего не найдено</p>
            </div>
          ) : (
            filteredAromas.map((aroma, index) => {
              const selectedVol = getVolumeForAroma(aroma.name);
              return (
                <div
                  key={index}
                  className="bg-gray-800 rounded-xl p-4 border border-gray-700 hover:border-gray-600 transition-colors"
                >
                  <div className="flex justify-between items-start mb-3">
                    <h3 className="font-semibold text-white text-lg">{aroma.name}</h3>
                    <span className="text-green-400 font-bold text-lg">
                      {aroma.prices[selectedVol] || '—'} ₽
                    </span>
                  </div>
                  
                  <div className="flex flex-wrap gap-2 mb-3">
                    {VOLUMES.map(vol => (
                      <button
                        key={vol}
                        onClick={() => setSelectedVolumes({
                          ...selectedVolumes,
                          [aroma.name]: vol,
                        })}
                        disabled={!aroma.prices[vol]}
                        className={`px-3 py-1.5 rounded-lg text-sm font-medium transition-all ${
                          selectedVol === vol
                            ? 'bg-purple-600 text-white shadow-lg shadow-purple-600/30'
                            : aroma.prices[vol]
                            ? 'bg-gray-700 text-gray-300 hover:bg-gray-600'
                            : 'bg-gray-700/30 text-gray-600 cursor-not-allowed'
                        }`}
                      >
                        {vol} ml
                      </button>
                    ))}
                  </div>

                  <button
                    onClick={() => addToCart(aroma, selectedVol)}
                    disabled={!aroma.prices[selectedVol]}
                    className="w-full bg-gradient-to-r from-purple-600 to-pink-600 hover:from-purple-700 hover:to-pink-700 disabled:from-gray-700 disabled:to-gray-700 disabled:text-gray-500 py-2.5 rounded-xl font-medium transition-all flex items-center justify-center gap-2"
                  >
                    <span>🛒</span>
                    <span>В корзину — {aroma.prices[selectedVol] || 0} ₽</span>
                  </button>
                </div>
              );
            })
          )}
        </div>

        {/* Floating Cart Summary */}
        {cart.length > 0 && !showCart && (
          <div className="fixed bottom-4 left-4 right-4 max-w-4xl mx-auto z-40">
            <button
              onClick={() => setShowCart(true)}
              className="w-full bg-gradient-to-r from-purple-600 to-pink-600 py-4 rounded-2xl flex items-center justify-between px-6 shadow-2xl shadow-purple-600/30 hover:from-purple-700 hover:to-pink-700 transition-all"
            >
              <div className="flex items-center gap-2">
                <span className="text-xl">🛒</span>
                <span className="font-medium">{getTotalItems()} товар(ов)</span>
              </div>
              <span className="text-xl font-bold">{getTotal()} ₽</span>
            </button>
          </div>
        )}
      </main>
    </div>
  );
}

export default App;


# ═══════════════════════════════════════════
# ФАЙЛ: public/manifest.json
# ═══════════════════════════════════════════

{
  "name": "Ароматы — Касса",
  "short_name": "Ароматы",
  "description": "Каталог ароматов с корзиной покупок",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#111827",
  "theme_color": "#7c3aed",
  "orientation": "portrait",
  "icons": [
    {
      "src": "/icon.svg",
      "sizes": "any",
      "type": "image/svg+xml"
    }
  ]
}


# ═══════════════════════════════════════════
# ФАЙЛ: public/icon.svg
# ═══════════════════════════════════════════

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 192 192">
  <defs>
    <linearGradient id="bg" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#1f2937"/>
      <stop offset="100%" style="stop-color:#111827"/>
    </linearGradient>
    <linearGradient id="bottle" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#a855f7"/>
      <stop offset="100%" style="stop-color:#ec4899"/>
    </linearGradient>
  </defs>
  <rect width="192" height="192" rx="40" fill="url(#bg)"/>
  <rect x="82" y="35" width="28" height="15" rx="4" fill="url(#bottle)" opacity="0.8"/>
  <rect x="76" y="50" width="40" height="8" rx="3" fill="url(#bottle)"/>
  <path d="M70 58 Q70 70 68 80 L68 145 Q68 160 83 160 L109 160 Q124 160 124 145 L124 80 Q122 70 122 58 Z" fill="url(#bottle)" opacity="0.9"/>
  <rect x="80" y="90" width="32" height="40" rx="4" fill="white" opacity="0.2"/>
</svg>


# ═══════════════════════════════════════════
# ИНСТРУКЦИЯ ПО СБОРКЕ APK
# ═══════════════════════════════════════════

## Что нужно установить:
1. Node.js 18+ → https://nodejs.org
2. Android Studio → https://developer.android.com/studio
3. Java JDK 17 → https://adoptium.net

## Команды для сборки:

```bash
# 1. Создайте папку проекта и скопируйте все файлы выше
mkdir aromas-app && cd aromas-app

# 2. Создайте структуру папок
mkdir -p src public

# 3. Скопируйте каждый файл в соответствующую папку

# 4. Установите зависимости
npm install

# 5. Соберите веб-приложение
npm run build

# 6. Добавьте Android платформу
npx cap add android

# 7. Синхронизируйте файлы
npx cap sync android

# 8. Откройте в Android Studio
npx cap open android
```

## В Android Studio:
1. Build → Build Bundle(s) / APK(s) → Build APK(s)
2. APK будет в: android/app/build/outputs/apk/debug/app-debug.apk
