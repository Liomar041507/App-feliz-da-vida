# App-feliz-da-vida
Feliz da vida
import React, { useState, useEffect } from "react";
import Button from "./components/Button";

const usuariosBase = [
  { id: 1, nome: "Maria (Pai)", tipo: "pai", senha: "1234" },
  { id: 2, nome: "Carlos (Professor)", tipo: "professor", senha: "1234" },
  { id: 3, nome: "Ana (Direção)", tipo: "direcao", senha: "1234" },
];

function AcessoNegado() {
  return (
    <div className="text-red-500 font-medium mt-4">
      🚫 Acesso negado. Você não tem permissão para esta área.
    </div>
  );
}

export default function App() {
  const [usuarioAtual, setUsuarioAtual] = useState(null);
  const [tela, setTela] = useState("login");

  // Persistência simples localStorage para lembrar login
  useEffect(() => {
    const user = localStorage.getItem("usuarioAtual");
    if (user) {
      setUsuarioAtual(JSON.parse(user));
      setTela("home");
    }
  }, []);

  useEffect(() => {
    if (usuarioAtual) localStorage.setItem("usuarioAtual", JSON.stringify(usuarioAtual));
    else localStorage.removeItem("usuarioAtual");
  }, [usuarioAtual]);

  const login = (nome, senha) => {
    const user = usuariosBase.find((u) => u.nome === nome && u.senha === senha);
    if (user) {
      setUsuarioAtual(user);
      setTela("home");
    } else alert("Usuário ou senha incorretos!");
  };

  const temPermissao = (tela) => {
    const tipo = usuarioAtual?.tipo;
    const permissoes = {
      avisos: ["pai", "professor", "direcao"],
      cardapio: ["pai", "professor", "direcao"],
      agenda: ["pai", "professor", "direcao"],
      fotos: ["pai", "professor", "direcao"],
      carne: ["pai", "direcao"],
      painel: ["direcao"],
    };
    return permissoes[tela]?.includes(tipo);
  };

  const screens = {
    login: (
      <div>
        <h2 className="text-lg font-semibold mb-4">Login</h2>
        {usuariosBase.map((user) => (
          <button
            key={user.id}
            className="block mb-2 px-4 py-2 bg-blue-600 text-white rounded"
            onClick={() => login(user.nome, "1234")}
          >
            {user.nome}
          </button>
        ))}
      </div>
    ),

    home: (
      <div>
        <h2 className="text-lg font-semibold mb-4">Olá, {usuarioAtual?.nome}</h2>
        <div className="grid grid-cols-2 gap-4">
          {temPermissao("avisos") && (
            <Button onClick={() => setTela("avisos")}>📢 Avisos</Button>
          )}
          {temPermissao("cardapio") && (
            <Button onClick={() => setTela("cardapio")}>🍽️ Cardápio</Button>
          )}
          {temPermissao("agenda") && (
            <Button onClick={() => setTela("agenda")}>📆 Agenda</Button>
          )}
          {temPermissao("fotos") && (
            <Button onClick={() => setTela("fotos")}>📷 Fotos</Button>
          )}
          {temPermissao("carne") && (
            <Button onClick={() => setTela("carne")}>💰 Carnê</Button>
          )}
          {temPermissao("painel") && (
            <Button
              onClick={() => setTela("painel")}
              variant="secondary"
            >
              ⚙️ Painel
            </Button>
          )}
          <Button
            onClick={() => {
              setUsuarioAtual(null);
              setTela("login");
            }}
            variant="outline"
          >
            🚪 Sair
          </Button>
        </div>
      </div>
    ),

    avisos: temPermissao("avisos") ? (
      <div>
        <h3 className="text-lg font-semibold">📢 Avisos</h3>
        <p>Conteúdo de avisos aqui.</p>
      </div>
    ) : (
      <AcessoNegado />
    ),

    cardapio: temPermissao("cardapio") ? (
      <div>
        <h3 className="text-lg font-semibold">🍽️ Cardápio</h3>
        <p>Cardápio da semana aqui.</p>
      </div>
    ) : (
      <AcessoNegado />
    ),

    agenda: temPermissao("agenda") ? (
      <div>
        <h3 className="text-lg font-semibold">📆 Agenda</h3>
        <p>Agenda diária personalizada.</p>
      </div>
    ) : (
      <AcessoNegado />
    ),

    fotos: temPermissao("fotos") ? (
      <div>
        <h3 className="text-lg font-semibold">📷 Galeria de Fotos</h3>
        <p>Fotos da creche.</p>
      </div>
    ) : (
      <AcessoNegado />
    ),

    carne: temPermissao("carne") ? (
      <div>
        <h3 className="text-lg font-semibold">💰 Carnê de Pagamento</h3>
        <p>Boletos e pagamentos.</p>
      </div>
    ) : (
      <AcessoNegado />
    ),

    painel: temPermissao("painel") ? (
      <div>
        <h3 className="text-lg font-semibold mb-4">⚙️ Painel Administrativo</h3>
        <p>Aqui você gerencia usuários e conteúdos.</p>
        {/* Implementação completa do painel fica aqui */}
      </div>
    ) : (
      <AcessoNegado />
    ),
  };

  return <div className="p-4 max-w-xl mx-auto">{screens[tela]}</div>;
}
