import React, { useState, useMemo, useRef } from "react";
import {
  LayoutDashboard, Receipt, Package, Users, Truck, BarChart3, Settings as SettingsIcon,
  Search, Plus, Minus, Trash2, Sun, Moon, Bell, ScanLine, Printer, Download, Share2,
  IndianRupee, TrendingUp, ShoppingBag, AlertTriangle, X, ChevronDown, CheckCircle2,
  Edit3, UserCircle2, Building2, Wallet, CreditCard, Smartphone, Split
} from "lucide-react";
import {
  ResponsiveContainer, AreaChart, Area, XAxis, YAxis, Tooltip, CartesianGrid,
  BarChart, Bar, PieChart, Pie, Cell, LineChart, Line, Legend
} from "recharts";

// ---------------------------------------------------------------------------
// DESIGN TOKENS
// ---------------------------------------------------------------------------
const FONTS_LINK = "https://fonts.googleapis.com/css2?family=Sora:wght@500;600;700;800&family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400;500;600;700&display=swap";

const palette = {
  light: {
    bg: "#FAF7F0", surface: "#FFFFFF", surfaceAlt: "#F2EDE3", border: "#E7E0D3",
    text: "#1F2A44", textSoft: "#6B7280", navBg: "#1F2A44", navText: "#D9DEEC",
    navTextActive: "#FFFFFF", navActiveBg: "#2C3B61",
  },
  dark: {
    bg: "#11141C", surface: "#181C27", surfaceAlt: "#1E2330", border: "#2A2F3D",
    text: "#EDEFF5", textSoft: "#9298A8", navBg: "#0C0E14", navText: "#9298A8",
    navTextActive: "#FFFFFF", navActiveBg: "#1F2A44",
  },
};
const accent = { saffron: "#E8862B", green: "#2F8F5B", red: "#C84B4B", blue: "#3B6FB6", purple: "#7A5FC9" };
const pieColors = [accent.saffron, accent.blue, accent.green, accent.purple, accent.red];

// ---------------------------------------------------------------------------
// SAMPLE DATA
// ---------------------------------------------------------------------------
const initialCategories = ["Groceries", "Beverages", "Snacks", "Personal Care", "Stationery", "Electronics"];

const initialProducts = [
  { id: "P001", name: "Tata Salt 1kg", sku: "GRC-001", barcode: "8901030591234", category: "Groceries", price: 28, cost: 22, gst: 5, stock: 142, reorder: 30, unit: "pkt" },
  { id: "P002", name: "Aashirvaad Atta 5kg", sku: "GRC-002", barcode: "8901491100123", category: "Groceries", price: 265, cost: 232, gst: 5, stock: 38, reorder: 20, unit: "bag" },
  { id: "P003", name: "Fortune Sunflower Oil 1L", sku: "GRC-003", barcode: "8901058000456", category: "Groceries", price: 168, cost: 150, gst: 5, stock: 64, reorder: 25, unit: "btl" },
  { id: "P004", name: "Amul Toned Milk 500ml", sku: "BEV-001", barcode: "8901262100789", category: "Beverages", price: 27, cost: 24, gst: 0, stock: 18, reorder: 25, unit: "pkt" },
  { id: "P005", name: "Coca-Cola 750ml", sku: "BEV-002", barcode: "8901011200345", category: "Beverages", price: 45, cost: 36, gst: 28, stock: 96, reorder: 30, unit: "btl" },
  { id: "P006", name: "Tata Tea Gold 250g", sku: "BEV-003", barcode: "8901030600234", category: "Beverages", price: 132, cost: 112, gst: 5, stock: 52, reorder: 20, unit: "pkt" },
  { id: "P007", name: "Lay's Classic 52g", sku: "SNK-001", barcode: "8901719110234", category: "Snacks", price: 20, cost: 15, gst: 12, stock: 210, reorder: 50, unit: "pkt" },
  { id: "P008", name: "Parle-G Biscuit 200g", sku: "SNK-002", barcode: "8901719100567", category: "Snacks", price: 20, cost: 16, gst: 18, stock: 8, reorder: 40, unit: "pkt" },
  { id: "P009", name: "Colgate Strong Teeth 200g", sku: "PC-001", barcode: "8901314000789", category: "Personal Care", price: 110, cost: 92, gst: 18, stock: 44, reorder: 15, unit: "tube" },
  { id: "P010", name: "Dove Soap 75g (Pack of 4)", sku: "PC-002", barcode: "8901030700890", category: "Personal Care", price: 196, cost: 168, gst: 18, stock: 12, reorder: 15, unit: "pack" },
  { id: "P011", name: "Classmate Notebook 200pg", sku: "STA-001", barcode: "8901406800123", category: "Stationery", price: 45, cost: 34, gst: 12, stock: 76, reorder: 20, unit: "pc" },
  { id: "P012", name: "boAt Rockerz 245 Earphones", sku: "ELE-001", barcode: "8904130900456", category: "Electronics", price: 899, cost: 650, gst: 18, stock: 6, reorder: 10, unit: "pc" },
];

const initialCustomers = [
  { id: "C001", name: "Rohit Sharma", phone: "9820012345", email: "rohit.sharma@example.com", gstin: "", address: "Andheri West, Mumbai", totalPurchases: 18420, lastVisit: "2026-06-18" },
  { id: "C002", name: "Priya Enterprises", phone: "9845098450", email: "accounts@priyaent.in", gstin: "27AAFCP1234L1Z5", address: "MG Road, Pune", totalPurchases: 64200, lastVisit: "2026-06-19" },
  { id: "C003", name: "Anjali Mehta", phone: "9911223344", email: "anjali.mehta@example.com", gstin: "", address: "Sector 18, Noida", totalPurchases: 5230, lastVisit: "2026-06-15" },
  { id: "C004", name: "Krishna Traders", phone: "9000011122", email: "krishna.traders@example.com", gstin: "29AAACK4321F1Z9", address: "Jayanagar, Bengaluru", totalPurchases: 112500, lastVisit: "2026-06-12" },
];

const initialSuppliers = [
  { id: "S001", name: "Sunrise Distributors", phone: "9876543210", email: "sales@sunrisedist.in", gstin: "27AAACS1111L1Z2", address: "Bhiwandi, Thane", category: "Groceries", outstanding: 24500 },
  { id: "S002", name: "Metro FMCG Supplies", phone: "9876511223", email: "orders@metrofmcg.in", gstin: "07AABCM2222M1Z3", address: "Okhla, Delhi", category: "Beverages & Snacks", outstanding: 0 },
  { id: "S003", name: "Hindustan Personal Care Co.", phone: "9988776655", email: "b2b@hpcco.in", gstin: "33AACHH3333N1Z4", address: "Guindy, Chennai", category: "Personal Care", outstanding: 8900 },
];

const shopProfile = {
  name: "Sharma General Store",
  address: "Shop No. 14, Lakshmi Market, Andheri West, Mumbai, Maharashtra - 400058",
  gstin: "27AABCS5678Q1Z6",
  phone: "+91 98200 12345",
  email: "sharmastore@example.com",
  state: "Maharashtra",
  stateCode: "27",
};

// past invoices for reports
const pastInvoices = [
  { id: "INV-2026-0142", date: "2026-06-13", customer: "Rohit Sharma", items: 4, subtotal: 980, discount: 20, gst: 86, total: 1046, profit: 198, payment: "UPI" },
  { id: "INV-2026-0143", date: "2026-06-14", customer: "Walk-in Customer", items: 2, subtotal: 245, discount: 0, gst: 21, total: 266, profit: 52, payment: "Cash" },
  { id: "INV-2026-0144", date: "2026-06-15", customer: "Anjali Mehta", items: 6, subtotal: 1540, discount: 50, gst: 142, total: 1632, profit: 310, payment: "Card" },
  { id: "INV-2026-0145", date: "2026-06-16", customer: "Walk-in Customer", items: 3, subtotal: 410, discount: 0, gst: 35, total: 445, profit: 88, payment: "Cash" },
  { id: "INV-2026-0146", date: "2026-06-17", customer: "Priya Enterprises", items: 12, subtotal: 8200, discount: 200, gst: 738, total: 8738, profit: 1640, payment: "Split" },
  { id: "INV-2026-0147", date: "2026-06-18", customer: "Rohit Sharma", items: 5, subtotal: 1120, discount: 0, gst: 98, total: 1218, profit: 224, payment: "UPI" },
  { id: "INV-2026-0148", date: "2026-06-19", customer: "Krishna Traders", items: 20, subtotal: 15400, discount: 400, gst: 1386, total: 16386, profit: 2980, payment: "Card" },
  { id: "INV-2026-0149", date: "2026-06-19", customer: "Walk-in Customer", items: 1, subtotal: 45, discount: 0, gst: 12, total: 57, profit: 9, payment: "Cash" },
];

const weeklyTrend = [
  { day: "Mon", sales: 9200, profit: 1680 },
  { day: "Tue", sales: 7400, profit: 1320 },
  { day: "Wed", sales: 11800, profit: 2150 },
  { day: "Thu", sales: 8600, profit: 1490 },
  { day: "Fri", sales: 13200, profit: 2480 },
  { day: "Sat", sales: 18900, profit: 3520 },
  { day: "Sun", sales: 15600, profit: 2890 },
];

const monthlyTrend = [
  { month: "Jan", sales: 312000 }, { month: "Feb", sales: 298000 }, { month: "Mar", sales: 341000 },
  { month: "Apr", sales: 356000 }, { month: "May", sales: 372000 }, { month: "Jun", sales: 214000 },
];

const categorySales = [
  { name: "Groceries", value: 42 }, { name: "Beverages", value: 21 }, { name: "Snacks", value: 14 },
  { name: "Personal Care", value: 13 }, { name: "Stationery", value: 6 }, { name: "Electronics", value: 4 },
];

const fmt = (n) => "\u20b9" + Number(n || 0).toLocaleString("en-IN", { maximumFractionDigits: 2, minimumFractionDigits: 2 });
const fmt0 = (n) => "\u20b9" + Number(n || 0).toLocaleString("en-IN", { maximumFractionDigits: 0 });

// ===========================================================================
// MAIN APP
// ===========================================================================
export default function ShopBillingPro() {
  const [theme, setTheme] = useState("light");
  const c = palette[theme];
  const [role, setRole] = useState("Admin"); // Admin | Manager | Cashier
  const [view, setView] = useState("dashboard");

  const [products, setProducts] = useState(initialProducts);
  const [categories] = useState(initialCategories);
  const [customers, setCustomers] = useState(initialCustomers);
  const [suppliers] = useState(initialSuppliers);
  const [invoices, setInvoices] = useState(pastInvoices);
  const [invoiceCounter, setInvoiceCounter] = useState(150);

  const [cart, setCart] = useState([]); // {productId, qty, discountPct}
  const [selectedCustomerId, setSelectedCustomerId] = useState("");
  const [interstate, setInterstate] = useState(false);
  const [paymentMode, setPaymentMode] = useState("Cash");
  const [splitAmounts, setSplitAmounts] = useState({ cash: "", upi: "", card: "" });
  const [search, setSearch] = useState("");
  const [barcodeInput, setBarcodeInput] = useState("");
  const [lastInvoice, setLastInvoice] = useState(null);
  const [showInvoiceModal, setShowInvoiceModal] = useState(false);
  const [printSize, setPrintSize] = useState("A4"); // A4 | 80mm | 58mm
  const [toast, setToast] = useState(null);
  const [showNotifs, setShowNotifs] = useState(false);
  const [productModal, setProductModal] = useState(null); // product being edited or "new"
  const [returnMode, setReturnMode] = useState(false);

  const navItems = [
    { id: "dashboard", label: "Dashboard", icon: LayoutDashboard, roles: ["Admin", "Manager", "Cashier"] },
    { id: "billing", label: "Billing / POS", icon: Receipt, roles: ["Admin", "Manager", "Cashier"] },
    { id: "products", label: "Products", icon: Package, roles: ["Admin", "Manager"] },
    { id: "customers", label: "Customers", icon: Users, roles: ["Admin", "Manager", "Cashier"] },
    { id: "suppliers", label: "Suppliers", icon: Truck, roles: ["Admin", "Manager"] },
    { id: "reports", label: "Reports", icon: BarChart3, roles: ["Admin", "Manager"] },
    { id: "settings", label: "Settings", icon: SettingsIcon, roles: ["Admin"] },
  ];

  const lowStock = products.filter((p) => p.stock <= p.reorder);

  const showToast = (msg, kind = "success") => {
    setToast({ msg, kind });
    setTimeout(() => setToast(null), 2600);
  };

  // -------------------- CART HELPERS --------------------
  const addToCart = (product) => {
    if (product.stock <= 0) { showToast(`${product.name} is out of stock`, "error"); return; }
    setCart((prev) => {
      const existing = prev.find((i) => i.productId === product.id);
      if (existing) {
        if (existing.qty + 1 > product.stock) { showToast("Not enough stock", "error"); return prev; }
        return prev.map((i) => (i.productId === product.id ? { ...i, qty: i.qty + 1 } : i));
      }
      return [...prev, { productId: product.id, qty: 1, discountPct: 0 }];
    });
  };
  const updateQty = (productId, delta) => {
    setCart((prev) =>
      prev
        .map((i) => {
          if (i.productId !== productId) return i;
          const prod = products.find((p) => p.id === productId);
          const newQty = i.qty + delta;
          if (newQty <= 0) return null;
          if (newQty > prod.stock) { showToast("Not enough stock", "error"); return i; }
          return { ...i, qty: newQty };
        })
        .filter(Boolean)
    );
  };
  const updateDiscount = (productId, val) => {
    setCart((prev) => prev.map((i) => (i.productId === productId ? { ...i, discountPct: Math.max(0, Math.min(100, Number(val) || 0)) } : i)));
  };
  const removeFromCart = (productId) => setCart((prev) => prev.filter((i) => i.productId !== productId));

  const cartLines = cart.map((i) => {
    const product = products.find((p) => p.id === i.productId);
    const gross = product.price * i.qty;
    const discountAmt = gross * (i.discountPct / 100);
    const taxable = gross - discountAmt;
    const gstAmt = taxable * (product.gst / 100);
    return { ...i, product, gross, discountAmt, taxable, gstAmt, lineTotal: taxable + gstAmt };
  });

  const cartTotals = cartLines.reduce(
    (acc, l) => ({
      subtotal: acc.subtotal + l.gross,
      discount: acc.discount + l.discountAmt,
      taxable: acc.taxable + l.taxable,
      gst: acc.gst + l.gstAmt,
      total: acc.total + l.lineTotal,
    }),
    { subtotal: 0, discount: 0, taxable: 0, gst: 0, total: 0 }
  );
  const cgst = interstate ? 0 : cartTotals.gst / 2;
  const sgst = interstate ? 0 : cartTotals.gst / 2;
  const igst = interstate ? cartTotals.gst : 0;

  const searchedProducts = products.filter((p) => {
    const q = search.toLowerCase();
    return !q || p.name.toLowerCase().includes(q) || p.sku.toLowerCase().includes(q) || p.barcode.includes(q);
  });

  const handleBarcodeSubmit = (e) => {
    e.preventDefault();
    const found = products.find((p) => p.barcode === barcodeInput.trim());
    if (found) { addToCart(found); showToast(`Added ${found.name}`); }
    else showToast("No product found for this barcode", "error");
    setBarcodeInput("");
  };

  const checkout = () => {
    if (cart.length === 0) { showToast("Cart is empty", "error"); return; }
    if (paymentMode === "Split") {
      const sum = (Number(splitAmounts.cash) || 0) + (Number(splitAmounts.upi) || 0) + (Number(splitAmounts.card) || 0);
      if (Math.round(sum) !== Math.round(cartTotals.total)) {
        showToast("Split amounts must add up to the total", "error"); return;
      }
    }
    const invNo = `INV-2026-${String(invoiceCounter).padStart(4, "0")}`;
    setInvoiceCounter((n) => n + 1);
    const customer = customers.find((c2) => c2.id === selectedCustomerId);
    const invoiceData = {
      id: invNo,
      date: new Date().toISOString().slice(0, 10),
      customer: customer ? customer.name : "Walk-in Customer",
      customerDetails: customer || null,
      lines: cartLines,
      subtotal: cartTotals.subtotal,
      discount: cartTotals.discount,
      taxable: cartTotals.taxable,
      cgst, sgst, igst,
      gst: cartTotals.gst,
      total: cartTotals.total,
      profit: cartLines.reduce((s, l) => s + (l.product.price - l.product.cost) * l.qty, 0),
      payment: paymentMode,
      interstate,
      items: cartLines.reduce((s, l) => s + l.qty, 0),
    };
    setInvoices((prev) => [...prev, invoiceData]);
    setProducts((prev) =>
      prev.map((p) => {
        const line = cartLines.find((l) => l.productId === p.id);
        return line ? { ...p, stock: p.stock - line.qty } : p;
      })
    );
    setLastInvoice(invoiceData);
    setShowInvoiceModal(true);
    setCart([]);
    setSelectedCustomerId("");
    setPaymentMode("Cash");
    setSplitAmounts({ cash: "", upi: "", card: "" });
    showToast(`Invoice ${invNo} generated`);
  };

  // -------------------- THEME WRAPPER STYLES --------------------
  const pageStyle = { background: c.bg, color: c.text, fontFamily: "Inter, sans-serif", minHeight: "100vh" };
  const cardStyle = { background: c.surface, border: `1px solid ${c.border}` };

  return (
    <div style={pageStyle} className="flex w-full text-sm">
      <style>{`
        @import url('${FONTS_LINK}');
        .font-display { font-family: 'Sora', sans-serif; }
        .font-mono-num { font-family: 'JetBrains Mono', monospace; }
        * { box-sizing: border-box; }
        .scrollbar-thin::-webkit-scrollbar { width: 6px; height: 6px; }
        .scrollbar-thin::-webkit-scrollbar-thumb { background: ${c.border}; border-radius: 4px; }
        @media print {
          .no-print { display: none !important; }
          .print-area { position: absolute; top:0; left:0; width:100%; }
        }
        .receipt-perforation {
          background-image: radial-gradient(circle at 6px 6px, ${c.bg} 5px, transparent 5px);
          background-size: 16px 12px;
          background-position: -4px 0;
          height: 12px;
        }
      `}</style>

      {/* ============================== SIDEBAR ============================== */}
      <aside className="no-print flex flex-col justify-between" style={{ width: 220, background: c.navBg, minHeight: "100vh" }}>
        <div>
          <div className="flex items-center gap-2 px-5 py-5">
            <div className="flex items-center justify-center rounded-lg" style={{ width: 34, height: 34, background: accent.saffron }}>
              <Receipt size={18} color="#1F2A44" />
            </div>
            <div>
              <div className="font-display font-bold text-white text-base leading-none">ShopBill Pro</div>
              <div className="text-xs" style={{ color: c.navText }}>India GST Edition</div>
            </div>
          </div>
          <nav className="mt-2 px-3 flex flex-col gap-1">
            {navItems.filter((n) => n.roles.includes(role)).map((n) => {
              const Icon = n.icon;
              const activeNav = view === n.id;
              return (
                <button
                  key={n.id}
                  onClick={() => setView(n.id)}
                  className="flex items-center gap-3 px-3 py-2.5 rounded-lg text-left transition-colors"
                  style={{
                    background: activeNav ? c.navActiveBg : "transparent",
                    color: activeNav ? c.navTextActive : c.navText,
                  }}
                >
                  <Icon size={17} />
                  <span className="text-sm font-medium">{n.label}</span>
                </button>
              );
            })}
          </nav>
        </div>
        <div className="px-4 py-4" style={{ borderTop: `1px solid rgba(255,255,255,0.08)` }}>
          <div className="text-xs mb-2" style={{ color: c.navText }}>Signed in as</div>
          <div className="flex items-center gap-2">
            <UserCircle2 size={28} color={c.navText} />
            <div>
              <div className="text-white text-sm font-medium">Store Owner</div>
              <select
                value={role}
                onChange={(e) => { setRole(e.target.value); setView("dashboard"); }}
                className="text-xs rounded px-1 py-0.5"
                style={{ background: c.navActiveBg, color: c.navText, border: "none" }}
              >
                <option>Admin</option>
                <option>Manager</option>
                <option>Cashier</option>
              </select>
            </div>
          </div>
        </div>
      </aside>

      {/* ============================== MAIN ============================== */}
      <main className="flex-1 min-h-screen">
        {/* TOP BAR */}
        <header className="no-print flex items-center justify-between px-6 py-4" style={{ borderBottom: `1px solid ${c.border}` }}>
          <div>
            <h1 className="font-display font-bold text-xl">{navItems.find((n) => n.id === view)?.label}</h1>
            <p className="text-xs" style={{ color: c.textSoft }}>{shopProfile.name} · GSTIN {shopProfile.gstin}</p>
          </div>
          <div className="flex items-center gap-3">
            <div className="relative">
              <button onClick={() => setShowNotifs((s) => !s)} className="relative p-2 rounded-lg" style={{ background: c.surfaceAlt }}>
                <Bell size={18} />
                {lowStock.length > 0 && (
                  <span className="absolute -top-1 -right-1 text-[10px] rounded-full w-4 h-4 flex items-center justify-center text-white" style={{ background: accent.red }}>
                    {lowStock.length}
                  </span>
                )}
              </button>
              {showNotifs && (
                <div className="absolute right-0 mt-2 w-72 rounded-xl shadow-lg z-20 p-3" style={cardStyle}>
                  <div className="font-display font-semibold text-sm mb-2 flex items-center gap-1">
                    <AlertTriangle size={14} color={accent.red} /> Low stock alerts
                  </div>
                  {lowStock.length === 0 && <p className="text-xs" style={{ color: c.textSoft }}>All stock levels healthy.</p>}
                  <div className="flex flex-col gap-2 max-h-56 overflow-y-auto scrollbar-thin">
                    {lowStock.map((p) => (
                      <div key={p.id} className="flex justify-between text-xs">
                        <span>{p.name}</span>
                        <span className="font-mono-num" style={{ color: accent.red }}>{p.stock} {p.unit} left</span>
                      </div>
                    ))}
                  </div>
                </div>
              )}
            </div>
            <button
              onClick={() => setTheme(theme === "light" ? "dark" : "light")}
              className="p-2 rounded-lg"
              style={{ background: c.surfaceAlt }}
              aria-label="Toggle theme"
            >
              {theme === "light" ? <Moon size={18} /> : <Sun size={18} />}
            </button>
          </div>
        </header>

        <div className="p-6">
          {view === "dashboard" && <Dashboard c={c} products={products} invoices={invoices} lowStock={lowStock} />}
          {view === "billing" && (
            <Billing
              c={c} products={products} categories={categories} customers={customers}
              search={search} setSearch={setSearch} searchedProducts={searchedProducts}
              addToCart={addToCart} cartLines={cartLines} updateQty={updateQty} updateDiscount={updateDiscount}
              removeFromCart={removeFromCart} cartTotals={cartTotals} cgst={cgst} sgst={sgst} igst={igst}
              interstate={interstate} setInterstate={setInterstate} selectedCustomerId={selectedCustomerId}
              setSelectedCustomerId={setSelectedCustomerId} paymentMode={paymentMode} setPaymentMode={setPaymentMode}
              splitAmounts={splitAmounts} setSplitAmounts={setSplitAmounts} barcodeInput={barcodeInput}
              setBarcodeInput={setBarcodeInput} handleBarcodeSubmit={handleBarcodeSubmit} checkout={checkout}
              returnMode={returnMode} setReturnMode={setReturnMode}
            />
          )}
          {view === "products" && <Products c={c} products={products} setProducts={setProducts} categories={categories} productModal={productModal} setProductModal={setProductModal} showToast={showToast} />}
          {view === "customers" && <Customers c={c} customers={customers} invoices={invoices} />}
          {view === "suppliers" && <Suppliers c={c} suppliers={suppliers} />}
          {view === "reports" && <Reports c={c} invoices={invoices} />}
          {view === "settings" && <SettingsView c={c} theme={theme} role={role} />}
        </div>
      </main>

      {/* ============================== INVOICE MODAL ============================== */}
      {showInvoiceModal && lastInvoice && (
        <InvoiceModal
          c={c} invoice={lastInvoice} onClose={() => setShowInvoiceModal(false)}
          printSize={printSize} setPrintSize={setPrintSize}
        />
      )}

      {/* ============================== TOAST ============================== */}
      {toast && (
        <div
          className="no-print fixed bottom-6 right-6 px-4 py-3 rounded-lg shadow-lg flex items-center gap-2 text-sm text-white z-50"
          style={{ background: toast.kind === "error" ? accent.red : accent.green }}
        >
          <CheckCircle2 size={16} /> {toast.msg}
        </div>
      )}
    </div>
  );
}

// ===========================================================================
// STAT CARD
// ===========================================================================
function StatCard({ c, icon: Icon, label, value, sub, color }) {
  return (
    <div className="rounded-2xl p-4 flex-1" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
      <div className="flex items-center justify-between mb-3">
        <div className="p-2 rounded-lg" style={{ background: color + "1A" }}>
          <Icon size={18} color={color} />
        </div>
      </div>
      <div className="font-mono-num font-bold text-2xl">{value}</div>
      <div className="text-xs mt-1" style={{ color: c.textSoft }}>{label}</div>
      {sub && <div className="text-xs mt-1 font-medium" style={{ color: accent.green }}>{sub}</div>}
    </div>
  );
}

// ===========================================================================
// DASHBOARD
// ===========================================================================
function Dashboard({ c, products, invoices, lowStock }) {
  const today = "2026-06-19";
  const todayInvoices = invoices.filter((i) => i.date === today);
  const todaySales = todayInvoices.reduce((s, i) => s + i.total, 0);
  const monthSales = invoices.reduce((s, i) => s + i.total, 0);
  const monthProfit = invoices.reduce((s, i) => s + (i.profit || 0), 0);
  const totalOrders = invoices.length;

  return (
    <div className="flex flex-col gap-6">
      <div className="flex gap-4 flex-wrap">
        <StatCard c={c} icon={IndianRupee} label="Today's Sales" value={fmt0(todaySales)} sub={todayInvoices.length + " orders today"} color={accent.saffron} />
        <StatCard c={c} icon={TrendingUp} label="Monthly Sales (Jun)" value={fmt0(monthSales)} color={accent.blue} />
        <StatCard c={c} icon={Wallet} label="Monthly Profit" value={fmt0(monthProfit)} sub={((monthProfit / Math.max(monthSales,1)) * 100).toFixed(1) + "% margin"} color={accent.green} />
        <StatCard c={c} icon={ShoppingBag} label="Total Orders" value={totalOrders} color={accent.purple} />
        <StatCard c={c} icon={Users} label="Customers" value={4} color={accent.red} />
      </div>

      <div className="grid grid-cols-3 gap-6">
        <div className="col-span-2 rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <h3 className="font-display font-semibold mb-4">This week — Sales vs Profit</h3>
          <ResponsiveContainer width="100%" height={260}>
            <AreaChart data={weeklyTrend}>
              <defs>
                <linearGradient id="sales" x1="0" y1="0" x2="0" y2="1">
                  <stop offset="0%" stopColor={accent.saffron} stopOpacity={0.4} />
                  <stop offset="100%" stopColor={accent.saffron} stopOpacity={0} />
                </linearGradient>
              </defs>
              <CartesianGrid strokeDasharray="3 3" stroke={c.border} />
              <XAxis dataKey="day" stroke={c.textSoft} fontSize={12} />
              <YAxis stroke={c.textSoft} fontSize={12} />
              <Tooltip contentStyle={{ background: c.surface, border: `1px solid ${c.border}`, fontSize: 12 }} formatter={(v) => fmt0(v)} />
              <Area type="monotone" dataKey="sales" stroke={accent.saffron} fill="url(#sales)" strokeWidth={2} />
              <Line type="monotone" dataKey="profit" stroke={accent.green} strokeWidth={2} dot={false} />
            </AreaChart>
          </ResponsiveContainer>
        </div>

        <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <h3 className="font-display font-semibold mb-4">Sales by category</h3>
          <ResponsiveContainer width="100%" height={220}>
            <PieChart>
              <Pie data={categorySales} dataKey="value" nameKey="name" innerRadius={45} outerRadius={75} paddingAngle={2}>
                {categorySales.map((entry, i) => <Cell key={entry.name} fill={pieColors[i % pieColors.length]} />)}
              </Pie>
              <Tooltip formatter={(v) => v + "%"} />
            </PieChart>
          </ResponsiveContainer>
          <div className="flex flex-wrap gap-2 mt-2">
            {categorySales.map((entry, i) => (
              <div key={entry.name} className="flex items-center gap-1 text-xs" style={{ color: c.textSoft }}>
                <span className="w-2 h-2 rounded-full inline-block" style={{ background: pieColors[i % pieColors.length] }} />
                {entry.name}
              </div>
            ))}
          </div>
        </div>
      </div>

      <div className="grid grid-cols-3 gap-6">
        <div className="col-span-2 rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <h3 className="font-display font-semibold mb-4">Recent invoices</h3>
          <table className="w-full text-xs">
            <thead>
              <tr style={{ color: c.textSoft }} className="text-left">
                <th className="py-2">Invoice</th><th>Customer</th><th>Items</th><th>Payment</th><th className="text-right">Total</th>
              </tr>
            </thead>
            <tbody className="font-mono-num">
              {invoices.slice(-6).reverse().map((inv) => (
                <tr key={inv.id} style={{ borderTop: `1px solid ${c.border}` }}>
                  <td className="py-2">{inv.id}</td>
                  <td className="font-sans">{inv.customer}</td>
                  <td>{inv.items}</td>
                  <td className="font-sans">{inv.payment}</td>
                  <td className="text-right font-semibold">{fmt(inv.total)}</td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
        <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <h3 className="font-display font-semibold mb-3 flex items-center gap-2">
            <AlertTriangle size={16} color={accent.red} /> Low stock
          </h3>
          <div className="flex flex-col gap-2 max-h-64 overflow-y-auto scrollbar-thin">
            {lowStock.map((p) => (
              <div key={p.id} className="flex justify-between items-center text-xs p-2 rounded-lg" style={{ background: c.surfaceAlt }}>
                <span>{p.name}</span>
                <span className="font-mono-num font-semibold" style={{ color: accent.red }}>{p.stock} {p.unit}</span>
              </div>
            ))}
            {lowStock.length === 0 && <p className="text-xs" style={{ color: c.textSoft }}>Nothing to reorder right now.</p>}
          </div>
        </div>
      </div>
    </div>
  );
}

// ===========================================================================
// BILLING / POS  — signature receipt-styled cart
// ===========================================================================
function Billing(props) {
  const {
    c, products, customers, search, setSearch, searchedProducts, addToCart,
    cartLines, updateQty, updateDiscount, removeFromCart, cartTotals, cgst, sgst, igst,
    interstate, setInterstate, selectedCustomerId, setSelectedCustomerId, paymentMode,
    setPaymentMode, splitAmounts, setSplitAmounts, barcodeInput, setBarcodeInput,
    handleBarcodeSubmit, checkout, returnMode, setReturnMode,
  } = props;

  return (
    <div className="grid grid-cols-3 gap-6">
      {/* PRODUCT PICKER */}
      <div className="col-span-2 flex flex-col gap-4">
        <div className="flex gap-3">
          <div className="flex-1 flex items-center gap-2 px-3 rounded-lg" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
            <Search size={16} color={c.textSoft} />
            <input
              value={search} onChange={(e) => setSearch(e.target.value)}
              placeholder="Search by name, SKU, or barcode…"
              className="w-full py-2.5 bg-transparent outline-none text-sm"
              style={{ color: c.text }}
            />
          </div>
          <form onSubmit={handleBarcodeSubmit} className="flex items-center gap-2 px-3 rounded-lg" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
            <ScanLine size={16} color={accent.saffron} />
            <input
              value={barcodeInput} onChange={(e) => setBarcodeInput(e.target.value)}
              placeholder="Scan barcode + Enter"
              className="py-2.5 bg-transparent outline-none text-sm w-44 font-mono-num"
              style={{ color: c.text }}
            />
          </form>
          <button
            onClick={() => setReturnMode((r) => !r)}
            className="px-4 rounded-lg text-sm font-medium flex items-center gap-2"
            style={{ background: returnMode ? accent.red : c.surfaceAlt, color: returnMode ? "white" : c.text }}
          >
            <Receipt size={15} /> {returnMode ? "Return mode: ON" : "Return / Refund"}
          </button>
        </div>

        <div className="grid grid-cols-3 gap-3 max-h-[560px] overflow-y-auto scrollbar-thin pr-1">
          {searchedProducts.map((p) => (
            <button
              key={p.id}
              onClick={() => addToCart(p)}
              className="text-left rounded-xl p-3 transition-transform hover:-translate-y-0.5"
              style={{ background: c.surface, border: `1px solid ${c.border}` }}
            >
              <div className="flex justify-between items-start mb-2">
                <span className="text-[10px] px-1.5 py-0.5 rounded font-mono-num" style={{ background: c.surfaceAlt, color: c.textSoft }}>{p.sku}</span>
                {p.stock <= p.reorder && <span className="text-[10px] font-semibold" style={{ color: accent.red }}>LOW</span>}
              </div>
              <div className="font-medium text-sm leading-snug mb-1">{p.name}</div>
              <div className="text-xs mb-2" style={{ color: c.textSoft }}>{p.category} · GST {p.gst}%</div>
              <div className="flex items-center justify-between">
                <span className="font-mono-num font-bold">{fmt(p.price)}</span>
                <span className="text-xs font-mono-num" style={{ color: c.textSoft }}>{p.stock} {p.unit}</span>
              </div>
            </button>
          ))}
        </div>
      </div>

      {/* RECEIPT-STYLE CART */}
      <div className="rounded-2xl overflow-hidden flex flex-col" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
        <div className="receipt-perforation" />
        <div className="px-5 pt-2 pb-4" style={{ borderBottom: `1px dashed ${c.border}` }}>
          <div className="text-center font-display font-bold">{shopProfile.name}</div>
          <div className="text-center text-[10px]" style={{ color: c.textSoft }}>GSTIN {shopProfile.gstin}</div>
        </div>

        <div className="px-5 py-3 flex flex-col gap-2" style={{ borderBottom: `1px dashed ${c.border}` }}>
          <select
            value={selectedCustomerId} onChange={(e) => setSelectedCustomerId(e.target.value)}
            className="text-sm rounded-lg px-2 py-2 outline-none"
            style={{ background: c.surfaceAlt, color: c.text, border: `1px solid ${c.border}` }}
          >
            <option value="">Walk-in Customer</option>
            {customers.map((cu) => <option key={cu.id} value={cu.id}>{cu.name}</option>)}
          </select>
          <label className="flex items-center gap-2 text-xs" style={{ color: c.textSoft }}>
            <input type="checkbox" checked={interstate} onChange={(e) => setInterstate(e.target.checked)} />
            Interstate sale (apply IGST instead of CGST+SGST)
          </label>
        </div>

        <div className="flex-1 overflow-y-auto scrollbar-thin px-5 py-3 font-mono-num text-xs" style={{ maxHeight: 280 }}>
          {cartLines.length === 0 && <p className="text-center py-10" style={{ color: c.textSoft }}>Cart is empty — tap a product to add it</p>}
          {cartLines.map((l) => (
            <div key={l.productId} className="mb-3 pb-2" style={{ borderBottom: `1px dotted ${c.border}` }}>
              <div className="flex justify-between font-sans font-medium text-sm">
                <span className="truncate pr-2">{l.product.name}</span>
                <button onClick={() => removeFromCart(l.productId)}><Trash2 size={13} color={accent.red} /></button>
              </div>
              <div className="flex items-center justify-between mt-1">
                <div className="flex items-center gap-1">
                  <button onClick={() => updateQty(l.productId, -1)} className="w-5 h-5 rounded flex items-center justify-center" style={{ background: c.surfaceAlt }}><Minus size={11} /></button>
                  <span className="w-6 text-center">{l.qty}</span>
                  <button onClick={() => updateQty(l.productId, 1)} className="w-5 h-5 rounded flex items-center justify-center" style={{ background: c.surfaceAlt }}><Plus size={11} /></button>
                  <span style={{ color: c.textSoft }}>× {fmt(l.product.price)}</span>
                </div>
                <span className="font-semibold">{fmt(l.lineTotal)}</span>
              </div>
              <div className="flex items-center gap-1 mt-1">
                <span style={{ color: c.textSoft }}>Disc %</span>
                <input
                  type="number" value={l.discountPct} min={0} max={100}
                  onChange={(e) => updateDiscount(l.productId, e.target.value)}
                  className="w-12 rounded px-1 py-0.5"
                  style={{ background: c.surfaceAlt, border: `1px solid ${c.border}`, color: c.text }}
                />
                <span style={{ color: c.textSoft }}>GST {l.product.gst}%</span>
              </div>
            </div>
          ))}
        </div>

        <div className="px-5 py-3 font-mono-num text-xs flex flex-col gap-1" style={{ borderTop: `1px dashed ${c.border}` }}>
          <Row label="Subtotal" value={fmt(cartTotals.subtotal)} c={c} />
          <Row label="Discount" value={"-" + fmt(cartTotals.discount)} c={c} />
          <Row label="Taxable value" value={fmt(cartTotals.taxable)} c={c} />
          {interstate ? <Row label="IGST" value={fmt(igst)} c={c} /> : (
            <>
              <Row label="CGST" value={fmt(cgst)} c={c} />
              <Row label="SGST" value={fmt(sgst)} c={c} />
            </>
          )}
          <div className="flex justify-between items-center pt-2 mt-1" style={{ borderTop: `1px solid ${c.border}` }}>
            <span className="font-display font-bold text-base">Total</span>
            <span className="font-display font-bold text-lg" style={{ color: accent.saffron }}>{fmt(cartTotals.total)}</span>
          </div>
        </div>

        <div className="px-5 py-3" style={{ borderTop: `1px dashed ${c.border}` }}>
          <div className="grid grid-cols-4 gap-1.5 mb-2">
            {[
              { id: "Cash", icon: Wallet }, { id: "UPI", icon: Smartphone },
              { id: "Card", icon: CreditCard }, { id: "Split", icon: Split },
            ].map((m) => (
              <button
                key={m.id} onClick={() => setPaymentMode(m.id)}
                className="flex flex-col items-center gap-1 py-2 rounded-lg text-[11px] font-medium"
                style={{ background: paymentMode === m.id ? accent.saffron : c.surfaceAlt, color: paymentMode === m.id ? "white" : c.text }}
              >
                <m.icon size={14} /> {m.id}
              </button>
            ))}
          </div>
          {paymentMode === "Split" && (
            <div className="grid grid-cols-3 gap-1.5 mb-2">
              {["cash", "upi", "card"].map((k) => (
                <input
                  key={k} type="number" placeholder={k} value={splitAmounts[k]}
                  onChange={(e) => setSplitAmounts((s) => ({ ...s, [k]: e.target.value }))}
                  className="text-xs rounded px-2 py-1.5 font-mono-num outline-none"
                  style={{ background: c.surfaceAlt, border: `1px solid ${c.border}`, color: c.text }}
                />
              ))}
            </div>
          )}
          <button
            onClick={checkout}
            className="w-full py-3 rounded-lg font-display font-semibold text-white"
            style={{ background: returnMode ? accent.red : accent.green }}
          >
            {returnMode ? "Process Return" : `Charge ${fmt(cartTotals.total)}`}
          </button>
        </div>
      </div>
    </div>
  );
}
function Row({ label, value, c }) {
  return <div className="flex justify-between"><span style={{ color: c.textSoft }}>{label}</span><span>{value}</span></div>;
}

// ===========================================================================
// PRODUCTS
// ===========================================================================
function Products({ c, products, setProducts, categories, productModal, setProductModal, showToast }) {
  const [q, setQ] = useState("");
  const filtered = products.filter((p) => p.name.toLowerCase().includes(q.toLowerCase()));

  const deleteProduct = (id) => {
    setProducts((prev) => prev.filter((p) => p.id !== id));
    showToast("Product deleted");
  };

  const saveProduct = (data) => {
    if (data.id && products.some((p) => p.id === data.id)) {
      setProducts((prev) => prev.map((p) => (p.id === data.id ? data : p)));
      showToast("Product updated");
    } else {
      const newId = "P" + String(products.length + 1).padStart(3, "0");
      setProducts((prev) => [...prev, { ...data, id: newId }]);
      showToast("Product added");
    }
    setProductModal(null);
  };

  return (
    <div className="flex flex-col gap-4">
      <div className="flex justify-between items-center">
        <div className="flex items-center gap-2 px-3 rounded-lg flex-1 max-w-sm" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <Search size={15} color={c.textSoft} />
          <input value={q} onChange={(e) => setQ(e.target.value)} placeholder="Search products…" className="w-full py-2 bg-transparent outline-none text-sm" />
        </div>
        <button onClick={() => setProductModal({ new: true })} className="flex items-center gap-2 px-4 py-2 rounded-lg text-white font-medium text-sm" style={{ background: accent.saffron }}>
          <Plus size={15} /> Add product
        </button>
      </div>

      <div className="rounded-2xl overflow-hidden" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
        <table className="w-full text-sm">
          <thead>
            <tr style={{ background: c.surfaceAlt, color: c.textSoft }} className="text-left text-xs">
              <th className="py-2.5 px-4">Product</th><th>SKU</th><th>Category</th><th>Price</th><th>GST%</th><th>Stock</th><th></th>
            </tr>
          </thead>
          <tbody>
            {filtered.map((p) => (
              <tr key={p.id} style={{ borderTop: `1px solid ${c.border}` }}>
                <td className="py-2.5 px-4 font-medium">{p.name}</td>
                <td className="font-mono-num text-xs" style={{ color: c.textSoft }}>{p.sku}</td>
                <td>{p.category}</td>
                <td className="font-mono-num">{fmt(p.price)}</td>
                <td className="font-mono-num">{p.gst}%</td>
                <td className="font-mono-num" style={{ color: p.stock <= p.reorder ? accent.red : c.text }}>{p.stock} {p.unit}</td>
                <td className="px-4">
                  <div className="flex gap-2 justify-end">
                    <button onClick={() => setProductModal(p)}><Edit3 size={14} color={accent.blue} /></button>
                    <button onClick={() => deleteProduct(p.id)}><Trash2 size={14} color={accent.red} /></button>
                  </div>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>

      {productModal && (
        <ProductModal c={c} categories={categories} product={productModal.new ? null : productModal} onClose={() => setProductModal(null)} onSave={saveProduct} />
      )}
    </div>
  );
}

function ProductModal({ c, categories, product, onClose, onSave }) {
  const [form, setForm] = useState(
    product || { id: "", name: "", sku: "", barcode: "", category: categories[0], price: "", cost: "", gst: 18, stock: "", reorder: 10, unit: "pc" }
  );
  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center" style={{ background: "rgba(0,0,0,0.45)" }}>
      <div className="rounded-2xl p-6 w-full max-w-md" style={{ background: c.surface }}>
        <div className="flex justify-between items-center mb-4">
          <h3 className="font-display font-semibold text-lg">{product ? "Edit product" : "Add product"}</h3>
          <button onClick={onClose}><X size={18} /></button>
        </div>
        <div className="grid grid-cols-2 gap-3 text-sm">
          <input placeholder="Product name" value={form.name} onChange={(e) => setForm({ ...form, name: e.target.value })} className="col-span-2 px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }} />
          <input placeholder="SKU" value={form.sku} onChange={(e) => setForm({ ...form, sku: e.target.value })} className="px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }} />
          <input placeholder="Barcode" value={form.barcode} onChange={(e) => setForm({ ...form, barcode: e.target.value })} className="px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }} />
          <select value={form.category} onChange={(e) => setForm({ ...form, category: e.target.value })} className="px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }}>
            {categories.map((cat) => <option key={cat}>{cat}</option>)}
          </select>
          <select value={form.unit} onChange={(e) => setForm({ ...form, unit: e.target.value })} className="px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }}>
            {["pc", "pkt", "bag", "btl", "tube", "pack"].map((u) => <option key={u}>{u}</option>)}
          </select>
          <input type="number" placeholder="Selling price" value={form.price} onChange={(e) => setForm({ ...form, price: Number(e.target.value) })} className="px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }} />
          <input type="number" placeholder="Cost price" value={form.cost} onChange={(e) => setForm({ ...form, cost: Number(e.target.value) })} className="px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }} />
          <select value={form.gst} onChange={(e) => setForm({ ...form, gst: Number(e.target.value) })} className="px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }}>
            {[0, 5, 12, 18, 28].map((g) => <option key={g} value={g}>GST {g}%</option>)}
          </select>
          <input type="number" placeholder="Stock qty" value={form.stock} onChange={(e) => setForm({ ...form, stock: Number(e.target.value) })} className="px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }} />
          <input type="number" placeholder="Reorder level" value={form.reorder} onChange={(e) => setForm({ ...form, reorder: Number(e.target.value) })} className="px-3 py-2 rounded-lg outline-none" style={{ background: c.surfaceAlt, border: `1px solid ${c.border}` }} />
        </div>
        <button onClick={() => onSave(form)} className="w-full mt-4 py-2.5 rounded-lg text-white font-medium" style={{ background: accent.saffron }}>Save product</button>
      </div>
    </div>
  );
}

// ===========================================================================
// CUSTOMERS
// ===========================================================================
function Customers({ c, customers, invoices }) {
  const [selected, setSelected] = useState(null);
  return (
    <div className="grid grid-cols-3 gap-4">
      <div className="col-span-2 rounded-2xl overflow-hidden" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
        <table className="w-full text-sm">
          <thead>
            <tr style={{ background: c.surfaceAlt, color: c.textSoft }} className="text-left text-xs">
              <th className="py-2.5 px-4">Name</th><th>Phone</th><th>GSTIN</th><th>Total purchases</th><th>Last visit</th>
            </tr>
          </thead>
          <tbody>
            {customers.map((cu) => (
              <tr key={cu.id} onClick={() => setSelected(cu)} style={{ borderTop: `1px solid ${c.border}`, cursor: "pointer" }} className="hover:opacity-80">
                <td className="py-2.5 px-4 font-medium">{cu.name}</td>
                <td className="font-mono-num">{cu.phone}</td>
                <td className="font-mono-num text-xs" style={{ color: c.textSoft }}>{cu.gstin || "—"}</td>
                <td className="font-mono-num">{fmt(cu.totalPurchases)}</td>
                <td className="font-mono-num text-xs">{cu.lastVisit}</td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
      <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
        <h3 className="font-display font-semibold mb-3">Purchase history</h3>
        {!selected && <p className="text-xs" style={{ color: c.textSoft }}>Click a customer to view history.</p>}
        {selected && (
          <div>
            <div className="font-medium mb-1">{selected.name}</div>
            <div className="text-xs mb-3" style={{ color: c.textSoft }}>{selected.address}</div>
            <div className="flex flex-col gap-2 max-h-64 overflow-y-auto scrollbar-thin">
              {invoices.filter((i) => i.customer === selected.name).map((inv) => (
                <div key={inv.id} className="flex justify-between text-xs p-2 rounded-lg" style={{ background: c.surfaceAlt }}>
                  <span className="font-mono-num">{inv.id}</span>
                  <span className="font-mono-num font-semibold">{fmt(inv.total)}</span>
                </div>
              ))}
              {invoices.filter((i) => i.customer === selected.name).length === 0 && <p className="text-xs" style={{ color: c.textSoft }}>No invoices yet for this customer.</p>}
            </div>
          </div>
        )}
      </div>
    </div>
  );
}

// ===========================================================================
// SUPPLIERS
// ===========================================================================
function Suppliers({ c, suppliers }) {
  return (
    <div className="grid grid-cols-3 gap-4">
      {suppliers.map((s) => (
        <div key={s.id} className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <div className="flex items-center gap-2 mb-3">
            <Building2 size={18} color={accent.blue} />
            <div className="font-display font-semibold">{s.name}</div>
          </div>
          <div className="text-xs flex flex-col gap-1" style={{ color: c.textSoft }}>
            <span>{s.category}</span>
            <span>{s.address}</span>
            <span className="font-mono-num">{s.phone}</span>
            <span className="font-mono-num">GSTIN: {s.gstin}</span>
          </div>
          <div className="mt-3 pt-3 flex justify-between items-center" style={{ borderTop: `1px solid ${c.border}` }}>
            <span className="text-xs" style={{ color: c.textSoft }}>Outstanding</span>
            <span className="font-mono-num font-semibold" style={{ color: s.outstanding > 0 ? accent.red : accent.green }}>
              {s.outstanding > 0 ? fmt(s.outstanding) : "Cleared"}
            </span>
          </div>
        </div>
      ))}
    </div>
  );
}

// ===========================================================================
// REPORTS
// ===========================================================================
function Reports({ c, invoices }) {
  const [tab, setTab] = useState("Daily");
  const tabs = ["Daily", "Weekly", "Monthly", "GST", "P&L", "Product-wise", "Customer"];

  const totalGst = invoices.reduce((s, i) => s + i.gst, 0);
  const totalSales = invoices.reduce((s, i) => s + i.total, 0);
  const totalProfit = invoices.reduce((s, i) => s + (i.profit || 0), 0);

  const byCustomer = useMemo(() => {
    const map = {};
    invoices.forEach((i) => { map[i.customer] = (map[i.customer] || 0) + i.total; });
    return Object.entries(map).map(([name, total]) => ({ name, total }));
  }, [invoices]);

  return (
    <div className="flex flex-col gap-4">
      <div className="flex gap-2 flex-wrap">
        {tabs.map((t) => (
          <button key={t} onClick={() => setTab(t)} className="px-3 py-1.5 rounded-lg text-xs font-medium" style={{ background: tab === t ? accent.saffron : c.surface, color: tab === t ? "white" : c.text, border: `1px solid ${c.border}` }}>{t}</button>
        ))}
      </div>

      {tab === "Daily" && (
        <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <h3 className="font-display font-semibold mb-3">Daily sales (last invoices)</h3>
          <table className="w-full text-xs">
            <thead><tr style={{ color: c.textSoft }} className="text-left"><th className="py-2">Date</th><th>Invoice</th><th>Items</th><th className="text-right">Total</th><th className="text-right">Profit</th></tr></thead>
            <tbody className="font-mono-num">
              {invoices.map((i) => (
                <tr key={i.id} style={{ borderTop: `1px solid ${c.border}` }}>
                  <td className="py-1.5">{i.date}</td><td>{i.id}</td><td>{i.items}</td>
                  <td className="text-right">{fmt(i.total)}</td>
                  <td className="text-right" style={{ color: accent.green }}>{fmt(i.profit || 0)}</td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      )}

      {tab === "Weekly" && (
        <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <h3 className="font-display font-semibold mb-3">Weekly sales trend</h3>
          <ResponsiveContainer width="100%" height={260}>
            <BarChart data={weeklyTrend}>
              <CartesianGrid strokeDasharray="3 3" stroke={c.border} />
              <XAxis dataKey="day" stroke={c.textSoft} fontSize={12} /><YAxis stroke={c.textSoft} fontSize={12} />
              <Tooltip formatter={(v) => fmt0(v)} contentStyle={{ background: c.surface, fontSize: 12 }} />
              <Bar dataKey="sales" fill={accent.saffron} radius={[4, 4, 0, 0]} />
            </BarChart>
          </ResponsiveContainer>
        </div>
      )}

      {tab === "Monthly" && (
        <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <h3 className="font-display font-semibold mb-3">Monthly sales (2026)</h3>
          <ResponsiveContainer width="100%" height={260}>
            <LineChart data={monthlyTrend}>
              <CartesianGrid strokeDasharray="3 3" stroke={c.border} />
              <XAxis dataKey="month" stroke={c.textSoft} fontSize={12} /><YAxis stroke={c.textSoft} fontSize={12} />
              <Tooltip formatter={(v) => fmt0(v)} contentStyle={{ background: c.surface, fontSize: 12 }} />
              <Line type="monotone" dataKey="sales" stroke={accent.blue} strokeWidth={2} />
            </LineChart>
          </ResponsiveContainer>
        </div>
      )}

      {tab === "GST" && (
        <div className="grid grid-cols-3 gap-4">
          <StatCard c={c} icon={IndianRupee} label="Total GST collected" value={fmt0(totalGst)} color={accent.saffron} />
          <StatCard c={c} icon={TrendingUp} label="Taxable turnover" value={fmt0(totalSales - totalGst)} color={accent.blue} />
          <StatCard c={c} icon={Receipt} label="Total invoices" value={invoices.length} color={accent.purple} />
          <div className="col-span-3 rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
            <p className="text-xs" style={{ color: c.textSoft }}>GSTR-1 / GSTR-3B summary export would be generated from this data in the full backend version (CGST + SGST for intrastate, IGST for interstate sales).</p>
          </div>
        </div>
      )}

      {tab === "P&L" && (
        <div className="grid grid-cols-3 gap-4">
          <StatCard c={c} icon={IndianRupee} label="Revenue" value={fmt0(totalSales)} color={accent.blue} />
          <StatCard c={c} icon={Wallet} label="Gross profit" value={fmt0(totalProfit)} color={accent.green} />
          <StatCard c={c} icon={BarChart3} label="Margin" value={((totalProfit / Math.max(totalSales,1)) * 100).toFixed(1) + "%"} color={accent.saffron} />
        </div>
      )}

      {tab === "Product-wise" && (
        <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <h3 className="font-display font-semibold mb-3">Sales by category (share of revenue)</h3>
          <ResponsiveContainer width="100%" height={260}>
            <BarChart data={categorySales} layout="vertical">
              <CartesianGrid strokeDasharray="3 3" stroke={c.border} />
              <XAxis type="number" stroke={c.textSoft} fontSize={12} /><YAxis dataKey="name" type="category" stroke={c.textSoft} fontSize={12} width={100} />
              <Tooltip formatter={(v) => v + "%"} />
              <Bar dataKey="value" fill={accent.purple} radius={[0, 4, 4, 0]} />
            </BarChart>
          </ResponsiveContainer>
        </div>
      )}

      {tab === "Customer" && (
        <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
          <h3 className="font-display font-semibold mb-3">Revenue by customer</h3>
          <table className="w-full text-xs">
            <thead><tr style={{ color: c.textSoft }} className="text-left"><th className="py-2">Customer</th><th className="text-right">Total spend</th></tr></thead>
            <tbody className="font-mono-num">
              {byCustomer.sort((a,b)=>b.total-a.total).map((row) => (
                <tr key={row.name} style={{ borderTop: `1px solid ${c.border}` }}>
                  <td className="py-1.5 font-sans">{row.name}</td><td className="text-right">{fmt(row.total)}</td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      )}
    </div>
  );
}

// ===========================================================================
// SETTINGS
// ===========================================================================
function SettingsView({ c, theme, role }) {
  return (
    <div className="grid grid-cols-2 gap-4 max-w-3xl">
      <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
        <h3 className="font-display font-semibold mb-3">Shop profile</h3>
        {Object.entries({ "Shop name": shopProfile.name, "GSTIN": shopProfile.gstin, "Phone": shopProfile.phone, "Email": shopProfile.email, "State": `${shopProfile.state} (${shopProfile.stateCode})` }).map(([k, v]) => (
          <div key={k} className="flex justify-between text-sm py-1.5" style={{ borderBottom: `1px solid ${c.border}` }}>
            <span style={{ color: c.textSoft }}>{k}</span><span className="font-medium">{v}</span>
          </div>
        ))}
      </div>
      <div className="rounded-2xl p-5" style={{ background: c.surface, border: `1px solid ${c.border}` }}>
        <h3 className="font-display font-semibold mb-3">App preferences</h3>
        <Row label="Theme" value={theme === "light" ? "Light" : "Dark"} c={c} />
        <Row label="Active role" value={role} c={c} />
        <Row label="Offline mode (PWA)" value="Enabled in production build" c={c} />
        <Row label="Cloud sync" value="Configured via backend API" c={c} />
        <button className="w-full mt-4 py-2.5 rounded-lg text-sm font-medium" style={{ background: c.surfaceAlt }}>Backup data (.json)</button>
        <button className="w-full mt-2 py-2.5 rounded-lg text-sm font-medium" style={{ background: c.surfaceAlt }}>Restore from backup</button>
      </div>
    </div>
  );
}

// ===========================================================================
// INVOICE MODAL — print / pdf / share
// ===========================================================================
function InvoiceModal({ c, invoice, onClose, printSize, setPrintSize }) {
  const widthMap = { A4: 620, "80mm": 320, "58mm": 240 };
  const shareText = encodeURIComponent(
    `${shopProfile.name}\nInvoice ${invoice.id}\nDate: ${invoice.date}\nTotal: ${fmt(invoice.total)}\nThank you for shopping with us!`
  );
  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center" style={{ background: "rgba(0,0,0,0.5)" }}>
      <div className="rounded-2xl flex flex-col" style={{ background: c.surface, maxHeight: "92vh" }}>
        <div className="no-print flex items-center justify-between px-5 py-3" style={{ borderBottom: `1px solid ${c.border}` }}>
          <div className="flex items-center gap-2">
            {["A4", "80mm", "58mm"].map((s) => (
              <button key={s} onClick={() => setPrintSize(s)} className="px-3 py-1.5 rounded-lg text-xs font-medium" style={{ background: printSize === s ? accent.saffron : c.surfaceAlt, color: printSize === s ? "white" : c.text }}>{s}</button>
            ))}
          </div>
          <div className="flex items-center gap-2">
            <button onClick={() => window.print()} className="flex items-center gap-1.5 px-3 py-1.5 rounded-lg text-xs font-medium" style={{ background: c.surfaceAlt }}><Printer size={13} /> Print</button>
            <button onClick={() => window.print()} className="flex items-center gap-1.5 px-3 py-1.5 rounded-lg text-xs font-medium" style={{ background: c.surfaceAlt }}><Download size={13} /> Save as PDF</button>
            <a href={`https://wa.me/?text=${shareText}`} target="_blank" rel="noreferrer" className="flex items-center gap-1.5 px-3 py-1.5 rounded-lg text-xs font-medium" style={{ background: accent.green, color: "white" }}><Share2 size={13} /> WhatsApp</a>
            <a href={`mailto:?subject=Invoice ${invoice.id}&body=${shareText}`} className="flex items-center gap-1.5 px-3 py-1.5 rounded-lg text-xs font-medium" style={{ background: c.surfaceAlt }}><Share2 size={13} /> Email</a>
            <button onClick={onClose}><X size={18} /></button>
          </div>
        </div>

        <div className="print-area overflow-y-auto scrollbar-thin p-6" style={{ width: widthMap[printSize] }}>
          <div className="font-mono-num text-xs" style={{ color: c.text }}>
            <div className="text-center mb-3">
              <div className="font-display font-bold text-base">{shopProfile.name}</div>
              <div>{shopProfile.address}</div>
              <div>GSTIN: {shopProfile.gstin} · {shopProfile.phone}</div>
            </div>
            <div className="flex justify-between py-2" style={{ borderTop: `1px dashed ${c.border}`, borderBottom: `1px dashed ${c.border}` }}>
              <span>Invoice: {invoice.id}</span><span>{invoice.date}</span>
            </div>
            <div className="py-2">
              <div>Bill to: {invoice.customer}</div>
              {invoice.customerDetails?.gstin && <div>GSTIN: {invoice.customerDetails.gstin}</div>}
              <div>Payment: {invoice.payment} {invoice.interstate ? "· Interstate (IGST)" : "· Intrastate (CGST+SGST)"}</div>
            </div>
            <table className="w-full" style={{ borderTop: `1px dashed ${c.border}`, borderBottom: `1px dashed ${c.border}` }}>
              <thead><tr className="text-left"><th className="py-1">Item</th><th className="text-right">Qty</th><th className="text-right">Rate</th><th className="text-right">GST</th><th className="text-right">Amt</th></tr></thead>
              <tbody>
                {invoice.lines.map((l) => (
                  <tr key={l.productId}>
                    <td className="py-1 pr-1">{l.product.name}</td>
                    <td className="text-right">{l.qty}</td>
                    <td className="text-right">{l.product.price.toFixed(0)}</td>
                    <td className="text-right">{l.product.gst}%</td>
                    <td className="text-right">{l.lineTotal.toFixed(2)}</td>
                  </tr>
                ))}
              </tbody>
            </table>
            <div className="pt-2 flex flex-col gap-0.5">
              <Row label="Subtotal" value={fmt(invoice.subtotal)} c={c} />
              <Row label="Discount" value={"-" + fmt(invoice.discount)} c={c} />
              {invoice.interstate ? <Row label="IGST" value={fmt(invoice.igst)} c={c} /> : (<><Row label="CGST" value={fmt(invoice.cgst)} c={c} /><Row label="SGST" value={fmt(invoice.sgst)} c={c} /></>)}
              <div className="flex justify-between font-bold text-sm pt-1 mt-1" style={{ borderTop: `1px solid ${c.border}` }}>
                <span>TOTAL</span><span>{fmt(invoice.total)}</span>
              </div>
            </div>
            <div className="text-center mt-4" style={{ color: c.textSoft }}>Thank you for shopping with us! · Goods once sold cannot be exchanged without this invoice.</div>
          </div>
        </div>
      </div>
    </div>
  );
}
