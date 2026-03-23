import React, { useMemo, useState } from "react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Badge } from "@/components/ui/badge";
import { Textarea } from "@/components/ui/textarea";
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogTrigger } from "@/components/ui/dialog";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Label } from "@/components/ui/label";
import { Progress } from "@/components/ui/progress";
import { motion } from "framer-motion";
import { Phone, CalendarDays, Users, MessageCircle, Plus, Search, Filter, CheckCircle2, Clock3, AlertCircle, Brain, HeartHandshake } from "lucide-react";

const statusOptions = [
  "Novo lead",
  "Em acolhimento",
  "Conversando",
  "Aguardando retorno",
  "Agendamento proposto",
  "Agendado",
  "Paciente ativo",
  "Reativar",
  "Encerrado",
];

const sourceOptions = ["Instagram", "WhatsApp", "Indicação", "Google", "Escola", "Outro"];
const serviceOptions = ["Avaliação TEA", "Avaliação TDAH", "Terapia", "Orientação parental", "Outro"];

const initialLeads = [
  {
    id: 1,
    nome: "Maria Silva",
    telefone: "(11) 99876-5432",
    cidade: "Tatuapé",
    origem: "Instagram",
    servico: "Avaliação TDAH",
    status: "Conversando",
    observacoes: "Relatou ansiedade e dificuldade de foco.",
    ultimaAcao: "Enviados valores e horários.",
    prioridade: "Alta",
  },
  {
    id: 2,
    nome: "Juliana Santos",
    telefone: "(11) 99123-4567",
    cidade: "Itaquaquecetuba",
    origem: "WhatsApp",
    servico: "Avaliação TEA",
    status: "Agendamento proposto",
    observacoes: "Busca atendimento para filho de 8 anos.",
    ultimaAcao: "Aguardando confirmação de horário.",
    prioridade: "Média",
  },
  {
    id: 3,
    nome: "Carlos Almeida",
    telefone: "(11) 97777-2222",
    cidade: "Online",
    origem: "Indicação",
    servico: "Terapia",
    status: "Agendado",
    observacoes: "Primeiro atendimento online.",
    ultimaAcao: "Consulta confirmada para amanhã.",
    prioridade: "Baixa",
  },
];

const agendaInicial = [
  { id: 1, hora: "09:00", nome: "Maria Silva", tipo: "Avaliação", modalidade: "Online", situacao: "Confirmado" },
  { id: 2, hora: "14:00", nome: "Juliana Santos", tipo: "Retorno", modalidade: "Presencial", situacao: "Pendente" },
  { id: 3, hora: "19:00", nome: "Carlos Almeida", tipo: "Terapia", modalidade: "Online", situacao: "Confirmado" },
];

function statusBadgeClass(status) {
  const map = {
    "Novo lead": "bg-rose-100 text-rose-700 border-rose-200",
    "Em acolhimento": "bg-pink-100 text-pink-700 border-pink-200",
    "Conversando": "bg-violet-100 text-violet-700 border-violet-200",
    "Aguardando retorno": "bg-amber-100 text-amber-700 border-amber-200",
    "Agendamento proposto": "bg-fuchsia-100 text-fuchsia-700 border-fuchsia-200",
    Agendado: "bg-emerald-100 text-emerald-700 border-emerald-200",
    "Paciente ativo": "bg-sky-100 text-sky-700 border-sky-200",
    Reativar: "bg-orange-100 text-orange-700 border-orange-200",
    Encerrado: "bg-slate-100 text-slate-600 border-slate-200",
  };
  return map[status] || "bg-slate-100 text-slate-700 border-slate-200";
}

function prioridadeBadgeClass(prioridade) {
  const map = {
    Alta: "bg-red-100 text-red-700",
    Média: "bg-yellow-100 text-yellow-700",
    Baixa: "bg-green-100 text-green-700",
  };
  return map[prioridade] || "bg-slate-100 text-slate-700";
}

export default function NeuroAcolheCRMApp() {
  const [leads, setLeads] = useState(initialLeads);
  const [agenda, setAgenda] = useState(agendaInicial);
  const [busca, setBusca] = useState("");
  const [filtroStatus, setFiltroStatus] = useState("todos");
  const [novoLead, setNovoLead] = useState({
    nome: "",
    telefone: "",
    cidade: "",
    origem: "WhatsApp",
    servico: "Avaliação TDAH",
    status: "Novo lead",
    observacoes: "",
    prioridade: "Média",
  });

  const leadsFiltrados = useMemo(() => {
    return leads.filter((lead) => {
      const matchBusca = [lead.nome, lead.telefone, lead.origem, lead.servico]
        .join(" ")
        .toLowerCase()
        .includes(busca.toLowerCase());
      const matchStatus = filtroStatus === "todos" ? true : lead.status === filtroStatus;
      return matchBusca && matchStatus;
    });
  }, [leads, busca, filtroStatus]);

  const totalLeads = leads.length;
  const agendados = leads.filter((l) => l.status === "Agendado").length;
  const emConversa = leads.filter((l) => ["Conversando", "Em acolhimento", "Agendamento proposto"].includes(l.status)).length;
  const pacientesAtivos = leads.filter((l) => l.status === "Paciente ativo").length;
  const conversao = totalLeads ? Math.round(((agendados + pacientesAtivos) / totalLeads) * 100) : 0;

  function adicionarLead() {
    if (!novoLead.nome || !novoLead.telefone) return;
    const lead = {
      id: Date.now(),
      ...novoLead,
      ultimaAcao: "Lead cadastrado no sistema.",
    };
    setLeads((prev) => [lead, ...prev]);
    setNovoLead({
      nome: "",
      telefone: "",
      cidade: "",
      origem: "WhatsApp",
      servico: "Avaliação TDAH",
      status: "Novo lead",
      observacoes: "",
      prioridade: "Média",
    });
  }

  function atualizarStatus(id, status) {
    setLeads((prev) => prev.map((lead) => (lead.id === id ? { ...lead, status, ultimaAcao: `Status atualizado para ${status}.` } : lead)));
  }

  return (
    <div className="min-h-screen bg-gradient-to-br from-rose-50 via-pink-50 to-violet-50 p-4 md:p-8">
      <div className="mx-auto max-w-7xl space-y-6">
        <motion.div
          initial={{ opacity: 0, y: 12 }}
          animate={{ opacity: 1, y: 0 }}
          className="flex flex-col gap-4 rounded-3xl bg-white/80 p-6 shadow-sm ring-1 ring-rose-100 backdrop-blur"
        >
          <div className="flex flex-col gap-3 md:flex-row md:items-center md:justify-between">
            <div>
              <div className="flex items-center gap-2 text-rose-700">
                <Brain className="h-5 w-5" />
                <span className="text-sm font-medium">NeuroAcolhe CRM</span>
              </div>
              <h1 className="mt-2 text-3xl font-bold tracking-tight text-rose-900">App de Leads + Agenda + WhatsApp</h1>
              <p className="mt-1 text-sm text-slate-600">Painel profissional para organizar contatos, agendamentos e evolução dos pacientes.</p>
            </div>
            <div className="flex items-center gap-3 rounded-2xl bg-rose-100 px-4 py-3 text-rose-800">
              <HeartHandshake className="h-5 w-5" />
              <div>
                <div className="text-xs uppercase tracking-wide">Padrão NeuroAcolhe</div>
                <div className="text-sm font-semibold">Rosa chá • acolhedor • profissional</div>
              </div>
            </div>
          </div>
        </motion.div>

        <div className="grid grid-cols-1 gap-4 md:grid-cols-2 xl:grid-cols-4">
          {[
            { titulo: "Leads", valor: totalLeads, icone: Users, subtitulo: "contatos cadastrados" },
            { titulo: "Em conversa", valor: emConversa, icone: MessageCircle, subtitulo: "em andamento" },
            { titulo: "Agendados", valor: agendados, icone: CalendarDays, subtitulo: "consultas fechadas" },
            { titulo: "Pacientes ativos", valor: pacientesAtivos, icone: CheckCircle2, subtitulo: "em acompanhamento" },
          ].map((card, i) => (
            <motion.div key={card.titulo} initial={{ opacity: 0, y: 8 }} animate={{ opacity: 1, y: 0 }} transition={{ delay: i * 0.05 }}>
              <Card className="rounded-3xl border-none bg-white/90 shadow-sm">
                <CardContent className="flex items-center justify-between p-5">
                  <div>
                    <p className="text-sm text-slate-500">{card.titulo}</p>
                    <h2 className="mt-1 text-3xl font-bold text-slate-800">{card.valor}</h2>
                    <p className="mt-1 text-xs text-slate-500">{card.subtitulo}</p>
                  </div>
                  <div className="rounded-2xl bg-rose-100 p-3 text-rose-700">
                    <card.icone className="h-6 w-6" />
                  </div>
                </CardContent>
              </Card>
            </motion.div>
          ))}
        </div>

        <Card className="rounded-3xl border-none bg-white/90 shadow-sm">
          <CardContent className="p-5">
            <div className="mb-3 flex items-center justify-between">
              <div>
                <h3 className="text-lg font-semibold text-slate-800">Conversão do funil</h3>
                <p className="text-sm text-slate-500">Percentual de leads que já viraram agendamento ou paciente.</p>
              </div>
              <Badge className="bg-rose-100 text-rose-700 hover:bg-rose-100">{conversao}%</Badge>
            </div>
            <Progress value={conversao} className="h-3" />
          </CardContent>
        </Card>

        <Tabs defaultValue="leads" className="space-y-4">
          <TabsList className="grid w-full grid-cols-4 rounded-2xl bg-white/90 p-1">
            <TabsTrigger value="leads" className="rounded-xl">Leads</TabsTrigger>
            <TabsTrigger value="agenda" className="rounded-xl">Agenda</TabsTrigger>
            <TabsTrigger value="mensagens" className="rounded-xl">Mensagens</TabsTrigger>
            <TabsTrigger value="dashboard" className="rounded-xl">Relatórios</TabsTrigger>
          </TabsList>

          <TabsContent value="leads" className="space-y-4">
            <Card className="rounded-3xl border-none bg-white/90 shadow-sm">
              <CardHeader className="flex flex-col gap-4 md:flex-row md:items-center md:justify-between">
                <div>
                  <CardTitle className="text-slate-800">Gestão de Leads</CardTitle>
                  <p className="text-sm text-slate-500">Cadastre, filtre e mova cada contato dentro do funil.</p>
                </div>
                <Dialog>
                  <DialogTrigger asChild>
                    <Button className="rounded-2xl bg-rose-600 hover:bg-rose-700"><Plus className="mr-2 h-4 w-4" />Novo lead</Button>
                  </DialogTrigger>
                  <DialogContent className="max-w-2xl rounded-3xl">
                    <DialogHeader>
                      <DialogTitle>Novo lead</DialogTitle>
                    </DialogHeader>
                    <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
                      <div className="space-y-2"><Label>Nome</Label><Input value={novoLead.nome} onChange={(e) => setNovoLead({ ...novoLead, nome: e.target.value })} /></div>
                      <div className="space-y-2"><Label>Telefone</Label><Input value={novoLead.telefone} onChange={(e) => setNovoLead({ ...novoLead, telefone: e.target.value })} /></div>
                      <div className="space-y-2"><Label>Cidade</Label><Input value={novoLead.cidade} onChange={(e) => setNovoLead({ ...novoLead, cidade: e.target.value })} /></div>
                      <div className="space-y-2">
                        <Label>Origem</Label>
                        <Select value={novoLead.origem} onValueChange={(v) => setNovoLead({ ...novoLead, origem: v })}>
                          <SelectTrigger><SelectValue /></SelectTrigger>
                          <SelectContent>{sourceOptions.map((s) => <SelectItem key={s} value={s}>{s}</SelectItem>)}</SelectContent>
                        </Select>
                      </div>
                      <div className="space-y-2">
                        <Label>Serviço</Label>
                        <Select value={novoLead.servico} onValueChange={(v) => setNovoLead({ ...novoLead, servico: v })}>
                          <SelectTrigger><SelectValue /></SelectTrigger>
                          <SelectContent>{serviceOptions.map((s) => <SelectItem key={s} value={s}>{s}</SelectItem>)}</SelectContent>
                        </Select>
                      </div>
                      <div className="space-y-2">
                        <Label>Prioridade</Label>
                        <Select value={novoLead.prioridade} onValueChange={(v) => setNovoLead({ ...novoLead, prioridade: v })}>
                          <SelectTrigger><SelectValue /></SelectTrigger>
                          <SelectContent>{["Alta", "Média", "Baixa"].map((s) => <SelectItem key={s} value={s}>{s}</SelectItem>)}</SelectContent>
                        </Select>
                      </div>
                      <div className="space-y-2 md:col-span-2"><Label>Observações</Label><Textarea value={novoLead.observacoes} onChange={(e) => setNovoLead({ ...novoLead, observacoes: e.target.value })} /></div>
                    </div>
                    <div className="mt-4 flex justify-end">
                      <Button onClick={adicionarLead} className="rounded-2xl bg-rose-600 hover:bg-rose-700">Salvar lead</Button>
                    </div>
                  </DialogContent>
                </Dialog>
              </CardHeader>
              <CardContent className="space-y-4">
                <div className="grid grid-cols-1 gap-3 md:grid-cols-[1fr_220px]">
                  <div className="relative">
                    <Search className="absolute left-3 top-3.5 h-4 w-4 text-slate-400" />
                    <Input value={busca} onChange={(e) => setBusca(e.target.value)} className="rounded-2xl pl-9" placeholder="Buscar por nome, telefone, origem ou serviço" />
                  </div>
                  <div>
                    <Select value={filtroStatus} onValueChange={setFiltroStatus}>
                      <SelectTrigger className="rounded-2xl"><Filter className="mr-2 h-4 w-4" /><SelectValue placeholder="Filtrar status" /></SelectTrigger>
                      <SelectContent>
                        <SelectItem value="todos">Todos os status</SelectItem>
                        {statusOptions.map((status) => <SelectItem key={status} value={status}>{status}</SelectItem>)}
                      </SelectContent>
                    </Select>
                  </div>
                </div>

                <div className="grid grid-cols-1 gap-4 xl:grid-cols-2">
                  {leadsFiltrados.map((lead) => (
                    <motion.div key={lead.id} whileHover={{ y: -2 }}>
                      <Card className="rounded-3xl border border-rose-100 bg-gradient-to-br from-white to-rose-50/50 shadow-sm">
                        <CardContent className="p-5 space-y-4">
                          <div className="flex items-start justify-between gap-3">
                            <div>
                              <h3 className="text-lg font-semibold text-slate-800">{lead.nome}</h3>
                              <div className="mt-1 flex items-center gap-2 text-sm text-slate-500">
                                <Phone className="h-4 w-4" /> {lead.telefone}
                              </div>
                            </div>
                            <div className="flex flex-col items-end gap-2">
                              <Badge className={`border ${statusBadgeClass(lead.status)}`}>{lead.status}</Badge>
                              <Badge className={prioridadeBadgeClass(lead.prioridade)}>{lead.prioridade}</Badge>
                            </div>
                          </div>

                          <div className="grid grid-cols-2 gap-3 text-sm">
                            <div className="rounded-2xl bg-white p-3 ring-1 ring-rose-100">
                              <div className="text-slate-400">Origem</div>
                              <div className="font-medium text-slate-700">{lead.origem}</div>
                            </div>
                            <div className="rounded-2xl bg-white p-3 ring-1 ring-rose-100">
                              <div className="text-slate-400">Serviço</div>
                              <div className="font-medium text-slate-700">{lead.servico}</div>
                            </div>
                          </div>

                          <div className="space-y-2 rounded-2xl bg-white p-4 ring-1 ring-rose-100">
                            <p className="text-sm"><span className="font-semibold text-slate-700">Observações:</span> <span className="text-slate-600">{lead.observacoes}</span></p>
                            <p className="text-sm"><span className="font-semibold text-slate-700">Última ação:</span> <span className="text-slate-600">{lead.ultimaAcao}</span></p>
                          </div>

                          <div className="flex flex-col gap-3 md:flex-row md:items-center md:justify-between">
                            <Select value={lead.status} onValueChange={(v) => atualizarStatus(lead.id, v)}>
                              <SelectTrigger className="w-full rounded-2xl md:w-[220px]"><SelectValue /></SelectTrigger>
                              <SelectContent>{statusOptions.map((status) => <SelectItem key={status} value={status}>{status}</SelectItem>)}</SelectContent>
                            </Select>
                            <div className="flex gap-2">
                              <Button variant="outline" className="rounded-2xl border-rose-200 text-rose-700">
                                <MessageCircle className="mr-2 h-4 w-4" /> WhatsApp
                              </Button>
                              <Button variant="outline" className="rounded-2xl border-violet-200 text-violet-700">
                                <CalendarDays className="mr-2 h-4 w-4" /> Agendar
                              </Button>
                            </div>
                          </div>
                        </CardContent>
                      </Card>
                    </motion.div>
                  ))}
                </div>
              </CardContent>
            </Card>
          </TabsContent>

          <TabsContent value="agenda">
            <Card className="rounded-3xl border-none bg-white/90 shadow-sm">
              <CardHeader>
                <CardTitle className="text-slate-800">Agenda do dia</CardTitle>
                <p className="text-sm text-slate-500">Visual simples para organizar avaliações, retornos e terapias.</p>
              </CardHeader>
              <CardContent className="space-y-3">
                {agenda.map((item) => (
                  <div key={item.id} className="flex flex-col gap-3 rounded-3xl border border-rose-100 bg-gradient-to-r from-white to-rose-50 p-4 md:flex-row md:items-center md:justify-between">
                    <div className="flex items-center gap-4">
                      <div className="rounded-2xl bg-rose-100 px-3 py-2 text-sm font-semibold text-rose-800">{item.hora}</div>
                      <div>
                        <h3 className="font-semibold text-slate-800">{item.nome}</h3>
                        <p className="text-sm text-slate-500">{item.tipo} • {item.modalidade}</p>
                      </div>
                    </div>
                    <Badge className={item.situacao === "Confirmado" ? "bg-emerald-100 text-emerald-700" : "bg-amber-100 text-amber-700"}>{item.situacao}</Badge>
                  </div>
                ))}
              </CardContent>
            </Card>
          </TabsContent>

          <TabsContent value="mensagens">
            <div className="grid grid-cols-1 gap-4 xl:grid-cols-2">
              {[
                { titulo: "Saudação inicial", texto: "Olá! Seja bem-vindo(a) ao NeuroAcolhe. Sou a Dra. Andrea. Me conte um pouquinho do que você está buscando."
                },
                { titulo: "Explicação do atendimento", texto: "Eu trabalho com avaliação completa e acompanhamento, sempre com olhar acolhedor e individualizado para cada caso." },
                { titulo: "Reativação", texto: "Oi, lembrei de você e do que me contou. Ainda posso te ajudar nesse processo?" },
                { titulo: "Confirmação", texto: "Seu horário está reservado. Se precisar remarcar, por favor me avise com antecedência." },
              ].map((msg) => (
                <Card key={msg.titulo} className="rounded-3xl border-none bg-white/90 shadow-sm">
                  <CardContent className="p-5 space-y-3">
                    <h3 className="font-semibold text-slate-800">{msg.titulo}</h3>
                    <div className="rounded-2xl bg-rose-50 p-4 text-sm text-slate-700 ring-1 ring-rose-100">{msg.texto}</div>
                    <Button variant="outline" className="rounded-2xl border-rose-200 text-rose-700">Copiar mensagem</Button>
                  </CardContent>
                </Card>
              ))}
            </div>
          </TabsContent>

          <TabsContent value="dashboard">
            <div className="grid grid-cols-1 gap-4 xl:grid-cols-3">
              <Card className="rounded-3xl border-none bg-white/90 shadow-sm xl:col-span-2">
                <CardContent className="p-5 space-y-4">
                  <h3 className="text-lg font-semibold text-slate-800">Resumo operacional</h3>
                  <div className="grid grid-cols-1 gap-3 md:grid-cols-3">
                    <div className="rounded-2xl bg-rose-50 p-4 ring-1 ring-rose-100">
                      <div className="flex items-center gap-2 text-rose-700"><Clock3 className="h-4 w-4" /> Pendentes</div>
                      <div className="mt-2 text-2xl font-bold text-slate-800">{leads.filter((l) => ["Novo lead", "Aguardando retorno"].includes(l.status)).length}</div>
                    </div>
                    <div className="rounded-2xl bg-violet-50 p-4 ring-1 ring-violet-100">
                      <div className="flex items-center gap-2 text-violet-700"><MessageCircle className="h-4 w-4" /> Em conversa</div>
                      <div className="mt-2 text-2xl font-bold text-slate-800">{emConversa}</div>
                    </div>
                    <div className="rounded-2xl bg-emerald-50 p-4 ring-1 ring-emerald-100">
                      <div className="flex items-center gap-2 text-emerald-700"><CheckCircle2 className="h-4 w-4" /> Convertidos</div>
                      <div className="mt-2 text-2xl font-bold text-slate-800">{agendados + pacientesAtivos}</div>
                    </div>
                  </div>
                </CardContent>
              </Card>

              <Card className="rounded-3xl border-none bg-white/90 shadow-sm">
                <CardContent className="p-5 space-y-4">
                  <h3 className="text-lg font-semibold text-slate-800">Alertas</h3>
                  <div className="space-y-3 text-sm text-slate-600">
                    <div className="flex gap-3 rounded-2xl bg-amber-50 p-3 ring-1 ring-amber-100"><AlertCircle className="mt-0.5 h-4 w-4 text-amber-600" /> 2 leads aguardando retorno.</div>
                    <div className="flex gap-3 rounded-2xl bg-rose-50 p-3 ring-1 ring-rose-100"><AlertCircle className="mt-0.5 h-4 w-4 text-rose-600" /> 1 agendamento precisa de confirmação.</div>
                    <div className="flex gap-3 rounded-2xl bg-sky-50 p-3 ring-1 ring-sky-100"><AlertCircle className="mt-0.5 h-4 w-4 text-sky-600" /> Hora de reativar leads parados.</div>
                  </div>
                </CardContent>
              </Card>
            </div>
          </TabsContent>
        </Tabs>
      </div>
    </div>
  );
}

