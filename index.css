import React, { useState, useEffect, useMemo } from 'react';
import { 
  LayoutDashboard, 
  User, 
  Briefcase, 
  PlusCircle, 
  Trash2, 
  Edit3, 
  CheckCircle, 
  XCircle,
  Calendar,
  ChevronLeft,
  ChevronRight,
  Wallet,
  TrendingDown,
  Clock,
  Search,
  Filter,
  Plus,
  MoreVertical,
  X,
  PieChart as PieChartIcon,
  BarChart as BarChartIcon
} from 'lucide-react';
import { motion, AnimatePresence } from 'motion/react';
import { 
  PieChart, 
  Pie, 
  Cell, 
  ResponsiveContainer, 
  Tooltip, 
  BarChart, 
  Bar, 
  XAxis, 
  YAxis, 
  CartesianGrid, 
  Legend 
} from 'recharts';

// --- Types ---

type TransactionType = 'pessoal' | 'profissional';
type TransactionStatus = 'pago' | 'pendente';

interface FinancialAccount {
  id: string;
  name: string;
  type: TransactionType;
}

interface Transaction {
  id: string;
  description: string;
  accountId: string; // Linked to FinancialAccount.id
  category: string;
  dueDate: string; // ISO string YYYY-MM-DD
  value: number;
  status: TransactionStatus;
  recurrent: boolean;
  type: TransactionType;
  installments?: number;
  currentInstallment?: number;
}

// --- Utils ---

const months = [
  "Janeiro", "Fevereiro", "Março", "Abril", "Maio", "Junho",
  "Julho", "Agosto", "Setembro", "Outubro", "Novembro", "Dezembro"
];

const formatCurrency = (value: number) => {
  return value.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });
};

const formatDate = (dateStr: string) => {
  return new Date(dateStr + 'T00:00:00').toLocaleDateString('pt-BR');
};

// --- Components ---

export default function App() {
  const [accounts, setAccounts] = useState<FinancialAccount[]>([]);
  const [activeAccountId, setActiveAccountId] = useState<string | null>(null);
  const [currentView, setCurrentView] = useState<'dashboard' | 'form'>('dashboard');
  const [subTab, setSubTab] = useState<'list' | 'analytics' | 'report'>('list');
  const [transactions, setTransactions] = useState<Transaction[]>([]);
  const [isEditing, setIsEditing] = useState<string | null>(null);
  const [selectedMonth, setSelectedMonth] = useState(new Date().getMonth());
  const [selectedYear, setSelectedYear] = useState(new Date().getFullYear());
  const [showAccountModal, setShowAccountModal] = useState<{type: TransactionType} | null>(null);
  const [newAccountName, setNewAccountName] = useState('');

  const activeAccount = useMemo(() => 
    accounts.find(a => a.id === activeAccountId), 
  [accounts, activeAccountId]);

  // Form State
  const [formData, setFormData] = useState({
    description: '',
    category: '',
    dueDate: new Date().toISOString().split('T')[0],
    value: '',
    status: 'pendente' as TransactionStatus,
    recurrent: false,
    installments: '1'
  });

  // Load from Storage
  useEffect(() => {
    const savedData = localStorage.getItem('finasync_data_v2');
    if (savedData) {
      try {
        const parsed = JSON.parse(savedData);
        setAccounts(parsed.accounts || []);
        setTransactions(parsed.transactions || []);
        if (parsed.accounts?.length > 0) setActiveAccountId(parsed.accounts[0].id);
      } catch (e) {
        console.error("Error loading data", e);
      }
    } else {
      setAccounts([]);
      setActiveAccountId(null);
    }
  }, []);

  // Save to Storage
  useEffect(() => {
    if (accounts.length > 0) {
      localStorage.setItem('finasync_data_v2', JSON.stringify({ accounts, transactions }));
    }
  }, [accounts, transactions]);

  // Handlers
  const handleAddAccount = () => {
    if (!newAccountName.trim() || !showAccountModal) return;
    const newAcc: FinancialAccount = {
      id: crypto.randomUUID(),
      name: newAccountName,
      type: showAccountModal.type
    };
    setAccounts([...accounts, newAcc]);
    setActiveAccountId(newAcc.id);
    setNewAccountName('');
    setShowAccountModal(null);
  };

  const deleteAccount = (id: string) => {
    if (window.confirm("Deseja excluir esta conta e todos os seus lançamentos?")) {
      const remainingAccounts = accounts.filter(a => a.id !== id);
      setAccounts(remainingAccounts);
      setTransactions(transactions.filter(t => t.accountId !== id));
      if (activeAccountId === id) {
        setActiveAccountId(remainingAccounts.length > 0 ? remainingAccounts[0].id : null);
      }
    }
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (!activeAccountId || !activeAccount) return;

    const valueNum = parseFloat(formData.value.toString().replace(',', '.'));
    if (isNaN(valueNum)) return;

    if (isEditing) {
      setTransactions(transactions.map(t => 
        t.id === isEditing ? { ...t, ...formData, value: valueNum, type: activeAccount.type, accountId: activeAccountId } : t
      ));
      setIsEditing(null);
    } else {
      const numInstallments = formData.recurrent ? parseInt(formData.installments) || 1 : 1;
      const newTransactions: Transaction[] = [];
      const baseDate = new Date(formData.dueDate + 'T00:00:00');

      for (let i = 0; i < numInstallments; i++) {
        const installmentDate = new Date(baseDate);
        installmentDate.setMonth(baseDate.getMonth() + i);
        
        const newTransaction: Transaction = {
          ...formData,
          id: crypto.randomUUID(),
          accountId: activeAccountId,
          value: valueNum,
          type: activeAccount.type,
          dueDate: installmentDate.toISOString().split('T')[0],
          installments: numInstallments > 1 ? numInstallments : undefined,
          currentInstallment: numInstallments > 1 ? i + 1 : undefined
        };
        newTransactions.push(newTransaction);
      }
      setTransactions([...newTransactions, ...transactions]);
    }

    setFormData({
      description: '',
      category: '',
      dueDate: new Date().toISOString().split('T')[0],
      value: '',
      status: 'pendente',
      recurrent: false,
      installments: '1'
    });
    setCurrentView('dashboard');
  };

  const deleteTransaction = (id: string) => {
    if (window.confirm("Deseja realmente excluir este lançamento?")) {
      setTransactions(transactions.filter(t => t.id !== id));
    }
  };

  const startEdit = (t: Transaction) => {
    setFormData({
      description: t.description,
      category: t.category,
      dueDate: t.dueDate,
      value: t.value.toString(),
      status: t.status,
      recurrent: t.recurrent,
      installments: t.installments?.toString() || '1'
    });
    setIsEditing(t.id);
    setCurrentView('form');
  };

  const toggleStatus = (id: string) => {
    setTransactions(transactions.map(t => 
      t.id === id ? { ...t, status: t.status === 'pago' ? 'pendente' : 'pago' } : t
    ));
  };

  // Filtered Lists
  const filteredTransactions = useMemo(() => {
    return transactions.filter(t => {
      const date = new Date(t.dueDate + 'T00:00:00');
      return t.accountId === activeAccountId && 
             date.getMonth() === selectedMonth && 
             date.getFullYear() === selectedYear;
    }).sort((a, b) => new Date(a.dueDate).getTime() - new Date(b.dueDate).getTime());
  }, [transactions, activeAccountId, selectedMonth, selectedYear]);

  // Statistics
  const stats = useMemo(() => {
    const paid = filteredTransactions
      .filter(t => t.status === 'pago')
      .reduce((acc, t) => acc + t.value, 0);
    const pending = filteredTransactions
      .filter(t => t.status === 'pendente')
      .reduce((acc, t) => acc + t.value, 0);
    return { paid, pending, total: paid + pending };
  }, [filteredTransactions]);

  // Chart Data
  const chartData = useMemo(() => {
    const categoryMap: Record<string, number> = {};
    filteredTransactions.forEach(t => {
      categoryMap[t.category] = (categoryMap[t.category] || 0) + t.value;
    });
    return Object.entries(categoryMap).map(([name, value]) => ({ name, value }));
  }, [filteredTransactions]);

  const COLORS = ['#D4AF37', '#1E293B', '#64748B', '#94A3B8', '#CBD5E1'];

  const changeMonth = (delta: number) => {
    let newMonth = selectedMonth + delta;
    let newYear = selectedYear;
    if (newMonth > 11) {
      newMonth = 0;
      newYear++;
    } else if (newMonth < 0) {
      newMonth = 11;
      newYear--;
    }
    setSelectedMonth(newMonth);
    setSelectedYear(newYear);
  };

  return (
    <div className="flex h-screen bg-[#F8FAFC] text-slate-800 font-sans selection:bg-blue-100">
      
      {/* SIDEBAR */}
      <aside className="w-64 bg-slate-900 text-white flex flex-col shadow-2xl relative z-10 overflow-hidden">
        <div className="p-8 border-b border-slate-800">
          <div className="flex items-center gap-3">
            <div className="w-10 h-10 bg-[#D4AF37] rounded-xl flex items-center justify-center shadow-lg shadow-[#D4AF37]/20">
              <Wallet className="text-slate-900" size={20} />
            </div>
            <div>
              <h1 className="text-xl font-bold tracking-tight text-[#D4AF37]">Controle Financeiro</h1>
              <p className="text-[10px] uppercase tracking-[0.2em] text-slate-400 font-medium">Gestão Inteligente</p>
            </div>
          </div>
        </div>

        <div className="flex-1 overflow-y-auto custom-scrollbar p-4 space-y-8">
          <div>
            <div className="flex items-center justify-between px-3 mb-2">
              <h3 className="text-xs font-bold text-slate-500 uppercase tracking-widest flex items-center gap-2">
                <User size={14} /> Pessoal
              </h3>
              <button 
                onClick={() => setShowAccountModal({type: 'pessoal'})}
                className="p-1 hover:bg-slate-800 rounded-md text-[#D4AF37] transition-colors"
                title="Adicionar Conta Pessoal"
              >
                <Plus size={14} />
              </button>
            </div>
            <div className="space-y-1">
              {accounts.filter(a => a.type === 'pessoal').map(acc => (
                <div key={acc.id} className="group relative">
                  <button 
                    onClick={() => { setActiveAccountId(acc.id); setCurrentView('dashboard'); }}
                    className={`w-full flex items-center gap-3 p-3 rounded-xl transition-all duration-300 font-medium text-sm text-left ${
                      activeAccountId === acc.id && currentView === 'dashboard'
                        ? 'bg-[#D4AF37] shadow-lg shadow-[#D4AF37]/40 text-slate-900' 
                        : 'text-slate-400 hover:bg-slate-800 hover:text-[#D4AF37]'
                    }`}
                  >
                    <span className="truncate flex-1">{acc.name}</span>
                  </button>
                  <button 
                    onClick={() => deleteAccount(acc.id)}
                    className="absolute right-2 top-1/2 -translate-y-1/2 p-1.5 opacity-0 group-hover:opacity-100 text-red-400 hover:text-red-500 transition-all"
                    title="Excluir Conta"
                  >
                    <Trash2 size={12} />
                  </button>
                </div>
              ))}
            </div>
          </div>

          <div>
            <div className="flex items-center justify-between px-3 mb-2">
              <h3 className="text-xs font-bold text-slate-500 uppercase tracking-widest flex items-center gap-2">
                <Briefcase size={14} /> Profissional
              </h3>
              <button 
                onClick={() => setShowAccountModal({type: 'profissional'})}
                className="p-1 hover:bg-slate-800 rounded-md text-[#D4AF37] transition-colors"
                title="Adicionar Conta Profissional"
              >
                <Plus size={14} />
              </button>
            </div>
            <div className="space-y-1">
              {accounts.filter(a => a.type === 'profissional').map(acc => (
                <div key={acc.id} className="group relative">
                  <button 
                    onClick={() => { setActiveAccountId(acc.id); setCurrentView('dashboard'); }}
                    className={`w-full flex items-center gap-3 p-3 rounded-xl transition-all duration-300 font-medium text-sm text-left ${
                      activeAccountId === acc.id && currentView === 'dashboard'
                        ? 'bg-[#D4AF37] shadow-lg shadow-[#D4AF37]/40 text-slate-900' 
                        : 'text-slate-400 hover:bg-slate-800 hover:text-[#D4AF37]'
                    }`}
                  >
                    <span className="truncate flex-1">{acc.name}</span>
                  </button>
                  <button 
                    onClick={() => deleteAccount(acc.id)}
                    className="absolute right-2 top-1/2 -translate-y-1/2 p-1.5 opacity-0 group-hover:opacity-100 text-red-400 hover:text-red-500 transition-all"
                    title="Excluir Conta"
                  >
                    <Trash2 size={12} />
                  </button>
                </div>
              ))}
            </div>
          </div>
        </div>

        <div className="p-6 border-t border-slate-800">
          <div className="bg-slate-800/50 p-4 rounded-xl border border-[#D4AF37]/10">
            <p className="text-xs text-slate-400 mb-1">Mês Selecionado</p>
            <p className="font-semibold text-[#D4AF37]">{months[selectedMonth]} {selectedYear}</p>
          </div>
        </div>
      </aside>

      {/* MODAL PARA NOVA CONTA */}
      <AnimatePresence>
        {showAccountModal && (
          <div className="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/60 backdrop-blur-sm">
            <motion.div 
              initial={{ scale: 0.9, opacity: 0 }}
              animate={{ scale: 1, opacity: 1 }}
              exit={{ scale: 0.9, opacity: 0 }}
              className="bg-white rounded-[32px] p-8 w-full max-w-md shadow-2xl"
            >
              <h3 className="text-xl font-bold text-slate-900 mb-2">Cadastrar Financeiro {showAccountModal.type}</h3>
              <p className="text-slate-500 text-sm mb-6">Dê um nome para esta nova conta de controle.</p>
              
              <input 
                type="text"
                autoFocus
                placeholder="Ex: Banco Itaú, Minha Loja, Caixa Reserva..."
                className="w-full bg-slate-50 border-2 border-transparent rounded-2xl p-4 focus:bg-white focus:border-[#D4AF37] outline-none transition-all placeholder:text-slate-300 font-medium mb-6"
                value={newAccountName}
                onChange={e => setNewAccountName(e.target.value)}
                onKeyDown={e => e.key === 'Enter' && handleAddAccount()}
              />

              <div className="flex gap-4">
                <button 
                  onClick={() => setShowAccountModal(null)}
                  className="flex-1 py-4 font-black text-slate-400 hover:text-slate-600 transition-colors uppercase tracking-widest text-xs"
                >
                  Cancelar
                </button>
                <button 
                  onClick={handleAddAccount}
                  className="flex-1 py-4 bg-slate-900 text-[#D4AF37] rounded-2xl font-black uppercase tracking-widest text-xs border border-[#D4AF37]/20 shadow-xl"
                >
                  Criar Conta
                </button>
              </div>
            </motion.div>
          </div>
        )}
      </AnimatePresence>

      {/* MAIN */}
      <main className="flex-1 flex flex-col overflow-hidden relative">
        
        {/* HEADER */}
        <header className="bg-white/80 backdrop-blur-md border-b border-slate-200 px-8 py-4 flex justify-between items-center sticky top-0 z-10">
          <div className="flex items-center gap-6">
            <div className="flex items-center gap-2 bg-slate-100 p-1 rounded-lg">
              <button 
                onClick={() => changeMonth(-1)}
                className="p-1.5 hover:bg-white rounded-md transition-colors text-slate-600"
              >
                <ChevronLeft size={18} />
              </button>
              <div className="px-4 font-semibold text-slate-700 min-w-[140px] text-center">
                {months[selectedMonth]} {selectedYear}
              </div>
              <button 
                onClick={() => changeMonth(1)}
                className="p-1.5 hover:bg-white rounded-md transition-colors text-slate-600"
              >
                <ChevronRight size={18} />
              </button>
            </div>
            <div className="h-4 w-px bg-slate-200 hidden md:block"></div>
            <h2 className="text-lg font-semibold text-slate-900 hidden md:block capitalize truncate max-w-[200px]">
              {activeAccount?.name || 'Visão Geral'}
            </h2>
          </div>

          <motion.button 
            whileHover={{ scale: 1.02 }}
            whileTap={{ scale: 0.98 }}
            onClick={() => { 
              if (currentView === 'form') {
                setCurrentView('dashboard');
                setIsEditing(null);
              } else {
                setCurrentView('form');
              }
            }}
            className={`flex items-center gap-2 px-5 py-2.5 rounded-xl font-bold shadow-lg transition-all ${
              currentView === 'form' && !isEditing 
                ? 'bg-slate-200 text-slate-700' 
                : 'bg-slate-900 text-[#D4AF37] hover:bg-slate-800 shadow-[#D4AF37]/10 border border-[#D4AF37]/20'
            }`}
          >
            {currentView === 'form' && !isEditing ? <X size={20} /> : <Plus size={20} />}
            <span>{currentView === 'form' && !isEditing ? 'Cancelar' : 'Nova Conta'}</span>
          </motion.button>
        </header>

        {/* SUB-NAV TABS */}
        <div className="bg-white px-8 border-b border-slate-100 flex items-center gap-8">
          <button 
            onClick={() => setSubTab('list')}
            className={`py-4 px-2 font-bold text-sm tracking-widest uppercase transition-all relative ${
              subTab === 'list' ? 'text-[#D4AF37]' : 'text-slate-400 hover:text-slate-600'
            }`}
          >
            Listagem de Contas
            {subTab === 'list' && (
              <motion.div layoutId="underline" className="absolute bottom-0 left-0 right-0 h-1 bg-[#D4AF37] rounded-full" />
            )}
          </button>
          <button 
            onClick={() => setSubTab('analytics')}
            className={`py-4 px-2 font-bold text-sm tracking-widest uppercase transition-all relative ${
              subTab === 'analytics' ? 'text-[#D4AF37]' : 'text-slate-400 hover:text-slate-600'
            }`}
          >
            Dashboard de Gastos
            {subTab === 'analytics' && (
              <motion.div layoutId="underline" className="absolute bottom-0 left-0 right-0 h-1 bg-[#D4AF37] rounded-full" />
            )}
          </button>
          <button 
            onClick={() => setSubTab('report')}
            className={`py-4 px-2 font-bold text-sm tracking-widest uppercase transition-all relative ${
              subTab === 'report' ? 'text-[#D4AF37]' : 'text-slate-400 hover:text-slate-600'
            }`}
          >
            Relatório / Planilha
            {subTab === 'report' && (
              <motion.div layoutId="underline" className="absolute bottom-0 left-0 right-0 h-1 bg-[#D4AF37] rounded-full" />
            )}
          </button>
        </div>

        {/* CONTENT */}
        <div className="flex-1 overflow-y-auto p-8 custom-scrollbar">
          {accounts.length === 0 ? (
            <motion.div 
              initial={{ opacity: 0, y: 20 }}
              animate={{ opacity: 1, y: 0 }}
              className="h-full flex flex-col items-center justify-center text-center max-w-2xl mx-auto space-y-8"
            >
              <div className="w-32 h-32 bg-[#D4AF37]/10 rounded-[40px] flex items-center justify-center text-[#D4AF37] shadow-xl shadow-[#D4AF37]/5 border border-[#D4AF37]/20">
                <Wallet size={64} strokeWidth={1} />
              </div>
              <div className="space-y-4">
                <h2 className="text-4xl font-black text-slate-900 tracking-tight">Bem-vindo ao FinaSync</h2>
                <p className="text-slate-500 text-lg font-medium leading-relaxed">
                  Para começar a organizar sua vida financeira, crie sua primeira conta no menu lateral à esquerda. Você pode separar o seu dinheiro <span className="text-blue-600 font-bold">Pessoal</span> do seu caixa <span className="text-emerald-600 font-bold">Profissional</span>.
                </p>
              </div>
              <div className="flex gap-4 pt-4">
                <button 
                  onClick={() => setShowAccountModal({type: 'pessoal'})}
                  className="px-8 py-4 bg-slate-900 text-[#D4AF37] rounded-2xl font-black uppercase tracking-widest text-xs border border-[#D4AF37]/20 shadow-2xl hover:bg-slate-800 transition-all flex items-center gap-3"
                >
                  <User size={18} /> Criar Conta Pessoal
                </button>
                <button 
                  onClick={() => setShowAccountModal({type: 'profissional'})}
                  className="px-8 py-4 bg-white text-slate-900 rounded-2xl font-black uppercase tracking-widest text-xs border border-slate-200 shadow-xl hover:bg-slate-50 transition-all flex items-center gap-3"
                >
                  <Briefcase size={18} /> Criar Conta Profissional
                </button>
              </div>
            </motion.div>
          ) : currentView === 'dashboard' ? (
            <motion.div
              initial={{ opacity: 0, y: 10 }}
              animate={{ opacity: 1, y: 0 }}
              className="space-y-8"
            >
              {subTab === 'analytics' ? (
                <>
                  {/* STATS SUMMARY */}
                  <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <StatCard 
                      label="Total de Gastos" 
                      value={stats.total} 
                      icon={<TrendingDown size={20} />} 
                      color="text-slate-900" 
                      bg="bg-white"
                    />
                    <StatCard 
                      label="Contas Pagas" 
                      value={stats.paid} 
                      icon={<CheckCircle size={20} />} 
                      color="text-emerald-600" 
                      bg="bg-emerald-50"
                    />
                    <StatCard 
                      label="Contas Pendentes" 
                      value={stats.pending} 
                      icon={<Clock size={20} />} 
                      color="text-amber-600" 
                      bg="bg-amber-50"
                    />
                  </div>

                  {/* DASHBOARD CHARTS */}
                  <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
                    <div className="bg-white p-8 rounded-[32px] shadow-xl shadow-slate-200/40 border border-slate-100">
                      <div className="flex items-center gap-2 mb-6">
                        <PieChartIcon size={18} className="text-[#D4AF37]" />
                        <h3 className="font-bold text-slate-800">Gastos por Categoria</h3>
                      </div>
                      <div className="h-[300px] w-full">
                        {chartData.length > 0 ? (
                          <ResponsiveContainer width="100%" height="100%">
                            <PieChart>
                              <Pie
                                data={chartData}
                                cx="50%"
                                cy="50%"
                                innerRadius={60}
                                outerRadius={100}
                                paddingAngle={5}
                                dataKey="value"
                              >
                                {chartData.map((entry, index) => (
                                  <Cell key={`cell-${index}`} fill={COLORS[index % COLORS.length]} />
                                ))}
                              </Pie>
                              <Tooltip 
                                contentStyle={{ borderRadius: '16px', border: 'none', boxShadow: '0 10px 15px -3px rgb(0 0 0 / 0.1)' }}
                                formatter={(value: number) => formatCurrency(value)}
                              />
                              <Legend verticalAlign="bottom" height={36}/>
                            </PieChart>
                          </ResponsiveContainer>
                        ) : (
                          <div className="h-full flex items-center justify-center text-slate-400 text-sm italic">
                            Sem dados para exibir este mês
                          </div>
                        )}
                      </div>
                    </div>

                    <div className="bg-white p-8 rounded-[32px] shadow-xl shadow-slate-200/40 border border-slate-100">
                      <div className="flex items-center gap-2 mb-6">
                        <BarChartIcon size={18} className="text-[#D4AF37]" />
                        <h3 className="font-bold text-slate-800">Status de Pagamento</h3>
                      </div>
                      <div className="h-[300px] w-full">
                        <ResponsiveContainer width="100%" height="100%">
                          <BarChart
                            data={[
                              { name: 'Pago', valor: stats.paid },
                              { name: 'Pendente', valor: stats.pending }
                            ]}
                            margin={{ top: 20, right: 30, left: 20, bottom: 5 }}
                          >
                            <CartesianGrid strokeDasharray="3 3" vertical={false} stroke="#F1F5F9" />
                            <XAxis 
                              dataKey="name" 
                              axisLine={false} 
                              tickLine={false} 
                              tick={{ fill: '#64748B', fontSize: 12, fontWeight: 600 }}
                            />
                            <YAxis 
                              axisLine={false} 
                              tickLine={false} 
                              tick={{ fill: '#64748B', fontSize: 10 }}
                              tickFormatter={(value) => `R$ ${value}`}
                            />
                            <Tooltip 
                              cursor={{ fill: '#F8FAFC' }}
                              contentStyle={{ borderRadius: '16px', border: 'none', boxShadow: '0 10px 15px -3px rgb(0 0 0 / 0.1)' }}
                              formatter={(value: number) => formatCurrency(value)}
                            />
                            <Bar dataKey="valor" radius={[10, 10, 0, 0]}>
                              <Cell fill="#10B981" />
                              <Cell fill="#F59E0B" />
                            </Bar>
                          </BarChart>
                        </ResponsiveContainer>
                      </div>
                    </div>
                  </div>
                </>
              ) : subTab === 'report' ? (
                <div className="space-y-6">
                  <div className="flex justify-between items-center no-print">
                    <h3 className="text-xl font-bold text-slate-800">Visualização de Planilha</h3>
                    <button 
                      onClick={() => window.print()}
                      className="flex items-center gap-2 px-6 py-3 bg-slate-900 text-[#D4AF37] rounded-xl font-bold border border-[#D4AF37]/20 shadow-xl hover:bg-slate-800 transition-all"
                    >
                      <PlusCircle size={18} className="rotate-45" /> Imprimir Relatório
                    </button>
                  </div>

                  <div className="bg-white p-12 rounded-[40px] shadow-2xl border border-slate-100 print-area">
                    <div className="flex justify-between items-start border-b-2 border-slate-900 pb-8 mb-8">
                      <div>
                        <h1 className="text-3xl font-black text-slate-900 uppercase">Relatório Financeiro</h1>
                        <p className="text-[#D4AF37] font-bold text-lg">{activeAccount?.name}</p>
                        <p className="text-slate-500 font-medium">Competência: {months[selectedMonth]} / {selectedYear}</p>
                      </div>
                      <div className="text-right">
                        <p className="text-slate-400 text-xs font-bold uppercase tracking-widest mb-1">Data de Emissão</p>
                        <p className="font-bold text-slate-900">{new Date().toLocaleDateString('pt-BR')}</p>
                      </div>
                    </div>

                    <div className="grid grid-cols-3 gap-8 mb-12">
                      <div className="p-4 bg-slate-50 rounded-2xl border border-slate-100">
                        <p className="text-[10px] font-black text-slate-400 uppercase mb-1">Total Previsto</p>
                        <p className="text-xl font-bold text-slate-900 font-mono">{formatCurrency(stats.total)}</p>
                      </div>
                      <div className="p-4 bg-emerald-50 rounded-2xl border border-emerald-100">
                        <p className="text-[10px] font-black text-emerald-400 uppercase mb-1">Total Pago</p>
                        <p className="text-xl font-bold text-emerald-600 font-mono">{formatCurrency(stats.paid)}</p>
                      </div>
                      <div className="p-4 bg-amber-50 rounded-2xl border border-amber-100">
                        <p className="text-[10px] font-black text-amber-400 uppercase mb-1">Total Pendente</p>
                        <p className="text-xl font-bold text-amber-600 font-mono">{formatCurrency(stats.pending)}</p>
                      </div>
                    </div>

                    <table className="w-full">
                      <thead>
                        <tr className="border-b-2 border-slate-200">
                          <th className="py-4 text-left font-black text-xs uppercase text-slate-900">Vencimento</th>
                          <th className="py-4 text-left font-black text-xs uppercase text-slate-900">Descrição</th>
                          <th className="py-4 text-left font-black text-xs uppercase text-slate-900">Categoria</th>
                          <th className="py-4 text-left font-black text-xs uppercase text-slate-900">Status</th>
                          <th className="py-4 text-right font-black text-xs uppercase text-slate-900">Valor (R$)</th>
                        </tr>
                      </thead>
                      <tbody>
                        {filteredTransactions.map(t => (
                          <tr key={t.id} className="border-b border-slate-100">
                            <td className="py-4 font-mono text-sm">{formatDate(t.dueDate)}</td>
                            <td className="py-4 font-bold text-slate-800">
                              {t.description}
                              {t.currentInstallment && ` (Parc. ${t.currentInstallment}/${t.installments})`}
                            </td>
                            <td className="py-4 text-slate-500 text-sm">{t.category}</td>
                            <td className="py-4">
                              <span className={`text-[10px] font-black uppercase ${t.status === 'pago' ? 'text-emerald-600' : 'text-amber-600'}`}>
                                {t.status}
                              </span>
                            </td>
                            <td className="py-4 text-right font-mono font-bold">{formatCurrency(t.value)}</td>
                          </tr>
                        ))}
                      </tbody>
                      <tfoot>
                        <tr>
                          <td colSpan={4} className="py-6 text-right font-black text-slate-900 uppercase">Total Geral</td>
                          <td className="py-6 text-right font-mono font-black text-2xl text-slate-900">{formatCurrency(stats.total)}</td>
                        </tr>
                      </tfoot>
                    </table>

                    <div className="mt-20 pt-8 border-t border-slate-100 text-center text-slate-300 text-[10px] font-bold uppercase tracking-widest">
                      Gerado pelo sistema de Controle Financeiro © {new Date().getFullYear()}
                    </div>
                  </div>
                </div>
              ) : (
                /* ACCOUNTS TABLE */
                <div className="bg-white rounded-3xl shadow-xl shadow-slate-200/40 border border-slate-100 overflow-hidden">
                  <div className="px-8 py-6 border-b border-slate-50 flex justify-between items-center">
                    <h3 className="font-bold text-slate-900 flex items-center gap-2">
                      <Filter size={18} className="text-slate-400" />
                      Listagem de Contas
                    </h3>
                    <div className="text-xs font-bold text-slate-400 uppercase tracking-widest">
                      {filteredTransactions.length} Registros
                    </div>
                  </div>

                  <div className="overflow-x-auto">
                    <table className="w-full text-left border-collapse">
                      <thead>
                        <tr className="border-b border-slate-50">
                          <th className="px-8 py-4 font-bold text-xs text-slate-400 uppercase tracking-widest">Descrição</th>
                          <th className="px-8 py-4 font-bold text-xs text-slate-400 uppercase tracking-widest">Categoria</th>
                          <th className="px-8 py-4 font-bold text-xs text-slate-400 uppercase tracking-widest">Vencimento</th>
                          <th className="px-8 py-4 font-bold text-xs text-slate-400 uppercase tracking-widest">Valor</th>
                          <th className="px-8 py-4 font-bold text-xs text-slate-400 uppercase tracking-widest">Status</th>
                          <th className="px-8 py-4 font-bold text-xs text-slate-400 uppercase tracking-widest text-center">Ações</th>
                        </tr>
                      </thead>
                      <tbody className="divide-y divide-slate-50">
                        <AnimatePresence mode="popLayout">
                          {filteredTransactions.map((t, index) => (
                            <motion.tr 
                              key={t.id}
                              initial={{ opacity: 0, x: -10 }}
                              animate={{ opacity: 1, x: 0 }}
                              exit={{ opacity: 0, scale: 0.95 }}
                              transition={{ delay: index * 0.03 }}
                              className="group hover:bg-blue-50/30 transition-all cursor-default"
                            >
                              <td className="px-8 py-5">
                                <div className="flex flex-col">
                                  <span className="font-bold text-slate-800">{t.description}</span>
                                  <div className="flex flex-wrap gap-2 items-center mt-1">
                                    {t.recurrent && (
                                      <span className="text-[10px] w-fit font-bold uppercase tracking-wider bg-indigo-50 text-indigo-500 px-1.5 py-0.5 rounded-md border border-indigo-100/50">
                                        Recorrente
                                      </span>
                                    )}
                                    {t.currentInstallment && t.installments && (
                                      <span className="text-[10px] w-fit font-bold uppercase tracking-wider bg-slate-100 text-slate-500 px-1.5 py-0.5 rounded-md border border-slate-200/50">
                                        Parcela {t.currentInstallment}/{t.installments}
                                      </span>
                                    )}
                                  </div>
                                </div>
                              </td>
                              <td className="px-8 py-5 text-slate-500 font-medium">{t.category}</td>
                              <td className="px-8 py-5 text-slate-600 font-medium">{formatDate(t.dueDate)}</td>
                              <td className="px-8 py-5 font-mono font-bold text-slate-900">{formatCurrency(t.value)}</td>
                              <td className="px-8 py-5">
                                <button 
                                  onClick={() => toggleStatus(t.id)}
                                  className={`flex items-center gap-2 px-4 py-1.5 rounded-full text-xs font-bold transition-all ${
                                    t.status === 'pago' 
                                      ? 'bg-emerald-100 text-emerald-700 hover:bg-emerald-200' 
                                      : 'bg-red-50 text-red-600 hover:bg-red-100 border border-red-100'
                                  }`}
                                >
                                  <span className={`w-1.5 h-1.5 rounded-full ${t.status === 'pago' ? 'bg-emerald-500' : 'bg-red-500'}`}></span>
                                  {t.status.toUpperCase()}
                                </button>
                              </td>
                              <td className="px-8 py-5">
                                <div className="flex justify-center gap-2 opacity-0 group-hover:opacity-100 transition-opacity">
                                  <button 
                                    onClick={() => startEdit(t)}
                                    className="w-10 h-10 flex items-center justify-center rounded-xl bg-blue-50 text-blue-600 hover:bg-blue-600 hover:text-white transition-all shadow-sm"
                                    title="Editar"
                                  >
                                    <Edit3 size={18} />
                                  </button>
                                  <button 
                                    onClick={() => deleteTransaction(t.id)}
                                    className="w-10 h-10 flex items-center justify-center rounded-xl bg-red-50 text-red-500 hover:bg-red-500 hover:text-white transition-all shadow-sm"
                                    title="Excluir"
                                  >
                                    <Trash2 size={18} />
                                  </button>
                                </div>
                              </td>
                            </motion.tr>
                          ))}
                        </AnimatePresence>
                        
                        {filteredTransactions.length === 0 && (
                          <motion.tr initial={{ opacity: 0 }} animate={{ opacity: 1 }}>
                            <td colSpan={6} className="px-8 py-20 text-center">
                              <div className="flex flex-col items-center gap-4 text-slate-300">
                                <div className="p-6 bg-slate-50 rounded-full">
                                  <Calendar size={48} strokeWidth={1} />
                                </div>
                                <div>
                                  <p className="text-slate-900 font-bold">Nenhum registro encontrado</p>
                                  <p className="text-sm font-medium">Não há contas cadastradas para este período ({months[selectedMonth]} {selectedYear}).</p>
                                </div>
                              </div>
                            </td>
                          </motion.tr>
                        )}
                      </tbody>
                    </table>
                  </div>
                </div>
              )}
            </motion.div>
          ) : (
            <motion.div 
              initial={{ opacity: 0, scale: 0.95 }}
              animate={{ opacity: 1, scale: 1 }}
              exit={{ opacity: 0, scale: 0.95 }}
              className="max-w-4xl mx-auto bg-white p-12 rounded-[48px] shadow-2xl shadow-slate-200/50 border border-slate-100 overflow-hidden relative"
            >
              <div className="flex flex-col mb-10">
                <h3 className="text-3xl font-black text-slate-900 tracking-tight mb-2">
                  {isEditing ? 'Editar Lançamento' : 'Novo Lançamento'}
                </h3>
                <p className="text-slate-500 font-medium italic">Preencha os detalhes da conta abaixo para o financeiro <span className="text-[#D4AF37] font-bold uppercase">{activeAccount?.name}</span>.</p>
              </div>
              
              <form onSubmit={handleSubmit} className="space-y-10">
                <div className="grid grid-cols-1 md:grid-cols-2 gap-8">
                  <div className="space-y-3">
                    <label className="text-sm font-black text-slate-800 uppercase tracking-widest px-1">Descrição</label>
                    <input 
                      type="text" placeholder="Ex: Aluguel, Internet..." required
                      className="w-full bg-slate-50 border-2 border-transparent rounded-2xl p-4 focus:bg-white focus:border-[#D4AF37] outline-none transition-all placeholder:text-slate-300 text-lg font-medium shadow-inner"
                      value={formData.description}
                      onChange={e => setFormData({...formData, description: e.target.value})}
                    />
                  </div>
                  <div className="space-y-3">
                    <label className="text-sm font-black text-slate-800 uppercase tracking-widest px-1">Categoria</label>
                    <input 
                      type="text" placeholder="Ex: Moradia, Lazer..." required
                      className="w-full bg-slate-50 border-2 border-transparent rounded-2xl p-4 focus:bg-white focus:border-[#D4AF37] outline-none transition-all placeholder:text-slate-300 text-lg font-medium shadow-inner"
                      value={formData.category}
                      onChange={e => setFormData({...formData, category: e.target.value})}
                    />
                  </div>
                  <div className="space-y-3">
                    <label className="text-sm font-black text-slate-800 uppercase tracking-widest px-1">Data de Vencimento</label>
                    <input 
                      type="date" required
                      className="w-full bg-slate-50 border-2 border-transparent rounded-2xl p-4 focus:bg-white focus:border-[#D4AF37] outline-none transition-all text-lg font-medium shadow-inner"
                      value={formData.dueDate}
                      onChange={e => setFormData({...formData, dueDate: e.target.value})}
                    />
                  </div>
                  <div className="space-y-3">
                    <label className="text-sm font-black text-slate-800 uppercase tracking-widest px-1">Valor do Título</label>
                    <div className="relative">
                      <span className="absolute left-5 top-1/2 -translate-y-1/2 text-slate-400 font-bold text-xl">R$</span>
                      <input 
                        type="text" placeholder="0,00" required
                        className="w-full bg-slate-50 border-2 border-transparent rounded-2xl p-4 pl-14 focus:bg-white focus:border-[#D4AF37] outline-none transition-all placeholder:text-slate-300 text-xl font-mono font-bold shadow-inner"
                        value={formData.value}
                        onChange={e => setFormData({...formData, value: e.target.value})}
                      />
                    </div>
                  </div>

                  <div className="space-y-3">
                    <label className="text-sm font-black text-slate-800 uppercase tracking-widest px-1">Status Inicial</label>
                    <div className="flex gap-4 p-1.5 bg-slate-50 rounded-2xl w-fit shadow-inner">
                      <button
                        type="button"
                        onClick={() => setFormData({...formData, status: 'pendente'})}
                        className={`px-6 py-2.5 rounded-[14px] font-bold text-sm transition-all ${
                          formData.status === 'pendente' 
                            ? 'bg-white shadow-md text-amber-600' 
                            : 'text-slate-400 hover:text-slate-600'
                        }`}
                      >
                        Pendente
                      </button>
                      <button
                        type="button"
                        onClick={() => setFormData({...formData, status: 'pago'})}
                        className={`px-6 py-2.5 rounded-[14px] font-bold text-sm transition-all ${
                          formData.status === 'pago' 
                            ? 'bg-white shadow-md text-emerald-600' 
                            : 'text-slate-400 hover:text-slate-600'
                        }`}
                      >
                        Pago
                      </button>
                    </div>
                  </div>

                  {formData.recurrent && (
                    <motion.div 
                      initial={{ opacity: 0, x: -20 }}
                      animate={{ opacity: 1, x: 0 }}
                      className="space-y-3"
                    >
                      <label className="text-sm font-black text-slate-800 uppercase tracking-widest px-1">Número de Parcelas</label>
                      <div className="flex items-center gap-3">
                        <input 
                          type="number" min="1" max="120"
                          className="w-32 bg-slate-50 border-2 border-transparent rounded-2xl p-4 focus:bg-white focus:border-[#D4AF37] outline-none transition-all text-xl font-bold font-mono shadow-inner text-center"
                          value={formData.installments}
                          onChange={e => setFormData({...formData, installments: e.target.value})}
                        />
                        <span className="text-slate-400 font-bold text-xs uppercase tracking-widest">Meses</span>
                      </div>
                    </motion.div>
                  )}
                </div>
                
                <div className="pt-6 border-t border-slate-100 flex flex-col md:flex-row items-center justify-between gap-8">
                  <label className="flex items-center gap-4 cursor-pointer group p-2 rounded-2xl hover:bg-slate-50 transition-colors">
                    <div className="relative flex items-center justify-center">
                      <input 
                        type="checkbox"
                        className="peer sr-only"
                        checked={formData.recurrent}
                        onChange={e => setFormData({...formData, recurrent: e.target.checked})}
                      />
                      <div className="w-14 h-8 bg-slate-200 rounded-full transition-all peer-checked:bg-[#D4AF37]"></div>
                      <div className="absolute left-1 w-6 h-6 bg-white rounded-full transition-all peer-checked:translate-x-6 shadow-md"></div>
                    </div>
                    <div className="flex flex-col">
                      <span className="text-base font-black text-slate-800 uppercase tracking-tight group-hover:text-slate-900">Conta Recorrente?</span>
                      <span className="text-xs text-slate-400 font-medium">Repete automaticamente todos os meses</span>
                    </div>
                  </label>

                  <div className="flex items-center gap-6 w-full md:w-auto">
                    <button 
                      type="button"
                      onClick={() => { setCurrentView('dashboard'); setIsEditing(null); }}
                      className="flex-1 md:flex-none px-8 py-4 font-black text-slate-400 hover:text-slate-600 transition-colors uppercase tracking-widest text-sm"
                    >
                      Descartar
                    </button>
                    <button 
                      type="submit" 
                      className={`flex-1 md:flex-none px-12 py-5 rounded-[20px] font-black transition-all shadow-2xl shadow-slate-900/10 text-base uppercase tracking-widest ${
                        isEditing 
                          ? 'bg-amber-500 text-white hover:bg-amber-600 shadow-amber-500/20' 
                          : 'bg-slate-900 text-[#D4AF37] hover:bg-slate-800 shadow-slate-900/40 border border-[#D4AF37]/30'
                      }`}
                    >
                      {isEditing ? 'Salvar Alterações' : 'Confirmar Lançamento'}
                    </button>
                  </div>
                </div>
              </form>
            </motion.div>
          )}
        </div>
      </main>
    </div>
  );
}

function StatCard({ label, value, icon, color, bg }: { label: string, value: number, icon: React.ReactNode, color: string, bg: string }) {
  return (
    <motion.div 
      whileHover={{ y: -4 }}
      className={`${bg} p-6 rounded-[32px] shadow-lg shadow-slate-200/20 border border-slate-100 flex flex-col justify-between h-32 overflow-hidden relative group`}
    >
      <div className="absolute -right-4 -bottom-4 text-slate-100/30 group-hover:text-slate-100/50 transition-colors pointer-events-none scale-[3]">
        {icon}
      </div>
      <div className="flex items-center gap-3 relative z-10">
        <div className={`p-2 rounded-xl bg-white shadow-sm border border-slate-100 ${color}`}>
          {icon}
        </div>
        <span className="text-xs font-bold text-slate-400 uppercase tracking-widest">{label}</span>
      </div>
      <div className={`text-2xl font-mono font-bold mt-2 relative z-10 ${color}`}>
        {formatCurrency(value)}
      </div>
    </motion.div>
  );
}
