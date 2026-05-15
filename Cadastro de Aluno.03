import { useState } from "react";

const initialAlunos = [];

const TURMAS = ["Turma A", "Turma B", "Turma C", "Turma D", "Outro"];

function Badge({ children, color }) {
  const colors = {
    green: "bg-emerald-100 text-emerald-700 border border-emerald-200",
    blue: "bg-sky-100 text-sky-700 border border-sky-200",
    amber: "bg-amber-100 text-amber-700 border border-amber-200",
    rose: "bg-rose-100 text-rose-700 border border-rose-200",
    violet: "bg-violet-100 text-violet-700 border border-violet-200",
  };
  return (
    <span className={`text-xs font-semibold px-2 py-0.5 rounded-full ${colors[color] || colors.blue}`}>
      {children}
    </span>
  );
}

function Modal({ title, onClose, children }) {
  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center p-4"
      style={{ background: "rgba(15,23,42,0.55)", backdropFilter: "blur(4px)" }}>
      <div className="bg-white rounded-2xl shadow-2xl w-full max-w-md animate-fade-in border border-slate-100">
        <div className="flex items-center justify-between px-6 pt-6 pb-4 border-b border-slate-100">
          <h2 className="text-lg font-bold text-slate-800 tracking-tight">{title}</h2>
          <button onClick={onClose}
            className="text-slate-400 hover:text-slate-600 text-2xl leading-none transition-colors">&times;</button>
        </div>
        <div className="px-6 py-5">{children}</div>
      </div>
    </div>
  );
}

function Toast({ msg, type }) {
  const styles = {
    success: "bg-emerald-500",
    error: "bg-rose-500",
    info: "bg-sky-500",
  };
  return (
    <div className={`fixed bottom-6 right-6 z-[100] px-5 py-3 rounded-xl text-white font-semibold shadow-xl text-sm transition-all ${styles[type] || styles.info}`}>
      {msg}
    </div>
  );
}

function InputField({ label, value, onChange, type = "text", placeholder, min, error }) {
  return (
    <div className="mb-4">
      <label className="block text-sm font-semibold text-slate-600 mb-1">{label}</label>
      <input
        type={type}
        value={value}
        onChange={onChange}
        placeholder={placeholder}
        min={min}
        className={`w-full border rounded-lg px-3 py-2 text-sm text-slate-800 bg-slate-50 focus:outline-none focus:ring-2 focus:ring-indigo-300 transition ${error ? "border-rose-400 bg-rose-50" : "border-slate-200"}`}
      />
      {error && <p className="text-xs text-rose-500 mt-1">{error}</p>}
    </div>
  );
}

function SelectField({ label, value, onChange, options }) {
  return (
    <div className="mb-4">
      <label className="block text-sm font-semibold text-slate-600 mb-1">{label}</label>
      <select value={value} onChange={onChange}
        className="w-full border border-slate-200 rounded-lg px-3 py-2 text-sm text-slate-800 bg-slate-50 focus:outline-none focus:ring-2 focus:ring-indigo-300 transition">
        <option value="">Selecione...</option>
        {options.map(o => <option key={o} value={o}>{o}</option>)}
      </select>
    </div>
  );
}

const turmaColors = ["green", "blue", "amber", "rose", "violet"];
function turmaColor(turma) {
  const idx = TURMAS.indexOf(turma);
  return turmaColors[idx % turmaColors.length] || "blue";
}

export default function SistemaEscolar() {
  const [alunos, setAlunos] = useState(initialAlunos);
  const [nextId, setNextId] = useState(1);
  const [view, setView] = useState("lista");
  const [toast, setToast] = useState(null);

  // Formulário cadastro/edição
  const [form, setForm] = useState({ nome: "", idade: "", turma: "" });
  const [formErrors, setFormErrors] = useState({});
  const [editingId, setEditingId] = useState(null);
  const [modalOpen, setModalOpen] = useState(false);

  // Busca
  const [busca, setBusca] = useState("");
  const [buscaResult, setBuscaResult] = useState(null);
  const [searched, setSearched] = useState(false);

  // Excluir
  const [confirmDelete, setConfirmDelete] = useState(null);

  function showToast(msg, type = "success") {
    setToast({ msg, type });
    setTimeout(() => setToast(null), 2800);
  }

  function validateForm() {
    const errs = {};
    if (!form.nome.trim()) errs.nome = "Nome é obrigatório.";
    const idade = parseInt(form.idade);
    if (!form.idade) errs.idade = "Idade é obrigatória.";
    else if (isNaN(idade) || idade < 18) errs.idade = "Idade mínima é 18 anos.";
    if (!form.turma) errs.turma = "Selecione uma turma.";
    return errs;
  }

  function handleCadastrar() {
    const errs = validateForm();
    if (Object.keys(errs).length) { setFormErrors(errs); return; }

    if (editingId !== null) {
      setAlunos(a => a.map(al => al.id === editingId
        ? { ...al, nome: form.nome.trim(), idade: parseInt(form.idade), turma: form.turma }
        : al));
      showToast("Aluno alterado com sucesso!");
    } else {
      setAlunos(a => [...a, { id: nextId, nome: form.nome.trim(), idade: parseInt(form.idade), turma: form.turma }]);
      setNextId(n => n + 1);
      showToast("Aluno cadastrado com sucesso!");
    }

    setForm({ nome: "", idade: "", turma: "" });
    setFormErrors({});
    setEditingId(null);
    setModalOpen(false);
  }

  function openEdit(aluno) {
    setForm({ nome: aluno.nome, idade: String(aluno.idade), turma: aluno.turma });
    setFormErrors({});
    setEditingId(aluno.id);
    setModalOpen(true);
  }

  function openCadastro() {
    setForm({ nome: "", idade: "", turma: "" });
    setFormErrors({});
    setEditingId(null);
    setModalOpen(true);
  }

  function handleExcluir(id) {
    setAlunos(a => a.filter(al => al.id !== id));
    setConfirmDelete(null);
    showToast("Aluno excluído com sucesso!", "error");
  }

  function handleBusca() {
    if (!busca.trim()) { setBuscaResult([]); setSearched(true); return; }
    const result = alunos.filter(a =>
      a.nome.toLowerCase().includes(busca.toLowerCase())
    );
    setBuscaResult(result);
    setSearched(true);
  }

  const navItems = [
    { id: "lista", label: "Listar Alunos", icon: "📋" },
    { id: "busca", label: "Localizar", icon: "🔍" },
  ];

  return (
    <div className="min-h-screen bg-slate-50 font-sans" style={{ fontFamily: "'Georgia', 'Times New Roman', serif" }}>
      {/* Header */}
      <div className="bg-white border-b border-slate-200 shadow-sm sticky top-0 z-40">
        <div className="max-w-5xl mx-auto px-4 py-4 flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="w-9 h-9 rounded-xl bg-indigo-600 flex items-center justify-center text-white text-lg shadow">🎓</div>
            <div>
              <div className="text-lg font-bold text-slate-800 leading-tight tracking-tight">Sistema Escolar</div>
              <div className="text-xs text-slate-400 font-normal" style={{ fontFamily: "sans-serif" }}>
                {alunos.length} aluno{alunos.length !== 1 ? "s" : ""} cadastrado{alunos.length !== 1 ? "s" : ""}
              </div>
            </div>
          </div>
          <button onClick={openCadastro}
            className="bg-indigo-600 hover:bg-indigo-700 text-white text-sm font-semibold px-4 py-2 rounded-xl shadow transition flex items-center gap-2"
            style={{ fontFamily: "sans-serif" }}>
            <span className="text-base">＋</span> Cadastrar Aluno
          </button>
        </div>
      </div>

      {/* Nav */}
      <div className="max-w-5xl mx-auto px-4 pt-6">
        <div className="flex gap-2 mb-6" style={{ fontFamily: "sans-serif" }}>
          {navItems.map(n => (
            <button key={n.id}
              onClick={() => { setView(n.id); setSearched(false); setBusca(""); setBuscaResult(null); }}
              className={`flex items-center gap-2 px-5 py-2 rounded-full text-sm font-semibold transition border
                ${view === n.id
                  ? "bg-indigo-600 text-white border-indigo-600 shadow"
                  : "bg-white text-slate-600 border-slate-200 hover:border-indigo-300 hover:text-indigo-600"}`}>
              <span>{n.icon}</span> {n.label}
            </button>
          ))}
        </div>

        {/* LISTA */}
        {view === "lista" && (
          <div>
            {alunos.length === 0 ? (
              <div className="flex flex-col items-center justify-center py-24 text-slate-400"
                style={{ fontFamily: "sans-serif" }}>
                <span className="text-5xl mb-4">📭</span>
                <div className="text-lg font-semibold">Nenhum aluno cadastrado</div>
                <div className="text-sm mt-1">Clique em "Cadastrar Aluno" para começar.</div>
              </div>
            ) : (
              <div className="bg-white rounded-2xl shadow border border-slate-100 overflow-hidden">
                <table className="w-full text-sm" style={{ fontFamily: "sans-serif" }}>
                  <thead>
                    <tr className="bg-slate-50 border-b border-slate-100">
                      <th className="px-5 py-3 text-left text-xs font-bold text-slate-400 uppercase tracking-widest w-12">ID</th>
                      <th className="px-5 py-3 text-left text-xs font-bold text-slate-400 uppercase tracking-widest">Nome</th>
                      <th className="px-5 py-3 text-left text-xs font-bold text-slate-400 uppercase tracking-widest w-20">Idade</th>
                      <th className="px-5 py-3 text-left text-xs font-bold text-slate-400 uppercase tracking-widest">Turma</th>
                      <th className="px-5 py-3 text-right text-xs font-bold text-slate-400 uppercase tracking-widest">Ações</th>
                    </tr>
                  </thead>
                  <tbody>
                    {alunos.map((al, i) => (
                      <tr key={al.id}
                        className={`border-b border-slate-50 transition hover:bg-indigo-50/40 ${i % 2 === 0 ? "" : "bg-slate-50/50"}`}>
                        <td className="px-5 py-3 text-slate-400 font-mono text-xs">{al.id}</td>
                        <td className="px-5 py-3 font-semibold text-slate-800">{al.nome}</td>
                        <td className="px-5 py-3 text-slate-600">{al.idade} anos</td>
                        <td className="px-5 py-3">
                          <Badge color={turmaColor(al.turma)}>{al.turma}</Badge>
                        </td>
                        <td className="px-5 py-3 text-right flex justify-end gap-2">
                          <button onClick={() => openEdit(al)}
                            className="text-xs bg-indigo-50 hover:bg-indigo-100 text-indigo-700 font-semibold px-3 py-1.5 rounded-lg transition">
                            ✏️ Editar
                          </button>
                          <button onClick={() => setConfirmDelete(al)}
                            className="text-xs bg-rose-50 hover:bg-rose-100 text-rose-600 font-semibold px-3 py-1.5 rounded-lg transition">
                            🗑️ Excluir
                          </button>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            )}
          </div>
        )}

        {/* BUSCA */}
        {view === "busca" && (
          <div style={{ fontFamily: "sans-serif" }}>
            <div className="bg-white rounded-2xl border border-slate-100 shadow p-6 mb-6">
              <div className="text-base font-bold text-slate-700 mb-3">🔍 Localizar Aluno pelo Nome</div>
              <div className="flex gap-3">
                <input
                  className="flex-1 border border-slate-200 rounded-xl px-4 py-2 text-sm bg-slate-50 focus:outline-none focus:ring-2 focus:ring-indigo-300"
                  placeholder="Digite o nome do aluno..."
                  value={busca}
                  onChange={e => { setBusca(e.target.value); setSearched(false); }}
                  onKeyDown={e => e.key === "Enter" && handleBusca()}
                />
                <button onClick={handleBusca}
                  className="bg-indigo-600 hover:bg-indigo-700 text-white text-sm font-semibold px-5 py-2 rounded-xl transition shadow">
                  Buscar
                </button>
              </div>
            </div>

            {searched && buscaResult !== null && (
              buscaResult.length === 0 ? (
                <div className="flex flex-col items-center py-12 text-slate-400">
                  <span className="text-4xl mb-3">🔎</span>
                  <div className="text-base font-semibold">Aluno não encontrado.</div>
                </div>
              ) : (
                <div className="space-y-3">
                  {buscaResult.map(al => (
                    <div key={al.id} className="bg-white rounded-2xl border border-slate-100 shadow p-5 flex items-center gap-4">
                      <div className="w-12 h-12 rounded-full bg-indigo-100 flex items-center justify-center text-2xl">🧑‍🎓</div>
                      <div className="flex-1">
                        <div className="font-bold text-slate-800 text-base">{al.nome}</div>
                        <div className="text-xs text-slate-400 mt-0.5">ID #{al.id} &middot; {al.idade} anos</div>
                      </div>
                      <Badge color={turmaColor(al.turma)}>{al.turma}</Badge>
                      <div className="flex gap-2">
                        <button onClick={() => openEdit(al)}
                          className="text-xs bg-indigo-50 hover:bg-indigo-100 text-indigo-700 font-semibold px-3 py-1.5 rounded-lg transition">
                          ✏️ Editar
                        </button>
                        <button onClick={() => setConfirmDelete(al)}
                          className="text-xs bg-rose-50 hover:bg-rose-100 text-rose-600 font-semibold px-3 py-1.5 rounded-lg transition">
                          🗑️ Excluir
                        </button>
                      </div>
                    </div>
                  ))}
                </div>
              )
            )}
          </div>
        )}
      </div>

      {/* MODAL CADASTRO/EDIÇÃO */}
      {modalOpen && (
        <Modal title={editingId !== null ? "Alterar Aluno" : "Cadastrar Aluno"} onClose={() => setModalOpen(false)}>
          <InputField
            label="Nome"
            value={form.nome}
            onChange={e => setForm(f => ({ ...f, nome: e.target.value }))}
            placeholder="Nome completo"
            error={formErrors.nome}
          />
          <InputField
            label="Idade (mínimo 18)"
            type="number"
            value={form.idade}
            onChange={e => setForm(f => ({ ...f, idade: e.target.value }))}
            placeholder="Idade"
            min={18}
            error={formErrors.idade}
          />
          <SelectField
            label="Turma"
            value={form.turma}
            onChange={e => setForm(f => ({ ...f, turma: e.target.value }))}
            options={TURMAS}
          />
          {formErrors.turma && <p className="text-xs text-rose-500 -mt-3 mb-3">{formErrors.turma}</p>}
          <div className="flex gap-3 mt-2" style={{ fontFamily: "sans-serif" }}>
            <button onClick={() => setModalOpen(false)}
              className="flex-1 border border-slate-200 rounded-xl py-2 text-sm font-semibold text-slate-600 hover:bg-slate-50 transition">
              Cancelar
            </button>
            <button onClick={handleCadastrar}
              className="flex-1 bg-indigo-600 hover:bg-indigo-700 text-white rounded-xl py-2 text-sm font-semibold transition shadow">
              {editingId !== null ? "Salvar Alterações" : "Cadastrar"}
            </button>
          </div>
        </Modal>
      )}

      {/* MODAL CONFIRMAÇÃO EXCLUIR */}
      {confirmDelete && (
        <Modal title="Confirmar Exclusão" onClose={() => setConfirmDelete(null)}>
          <div className="text-center" style={{ fontFamily: "sans-serif" }}>
            <div className="text-4xl mb-3">⚠️</div>
            <div className="text-slate-700 font-semibold mb-1">Deseja excluir o aluno?</div>
            <div className="text-slate-500 text-sm mb-6">
              <span className="font-bold text-slate-700">{confirmDelete.nome}</span> será removido permanentemente.
            </div>
            <div className="flex gap-3">
              <button onClick={() => setConfirmDelete(null)}
                className="flex-1 border border-slate-200 rounded-xl py-2 text-sm font-semibold text-slate-600 hover:bg-slate-50 transition">
                Cancelar
              </button>
              <button onClick={() => handleExcluir(confirmDelete.id)}
                className="flex-1 bg-rose-500 hover:bg-rose-600 text-white rounded-xl py-2 text-sm font-semibold transition shadow">
                Sim, excluir
              </button>
            </div>
          </div>
        </Modal>
      )}

      {/* TOAST */}
      {toast && <Toast msg={toast.msg} type={toast.type} />}

      <style>{`
        @keyframes fade-in {
          from { opacity: 0; transform: scale(0.97) translateY(10px); }
          to { opacity: 1; transform: scale(1) translateY(0); }
        }
        .animate-fade-in { animation: fade-in 0.18s ease; }
      `}</style>
    </div>
  );
}
