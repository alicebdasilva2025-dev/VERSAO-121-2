# LiDire — Versão 121

## Correção solicitada
- Removida a inclusão automática de painéis “compartilhados comigo” de todas as abas individuais de recursos (Agenda, Compras, Estudos, Treinos, Hidratação, Alimentação, Finanças, Objetivos, Lembretes e demais módulos mapeados).
- A alteração é apenas de apresentação: não apaga registros compartilhados, não modifica permissões e não altera endpoints/API nem o banco D1.
- As atualizações dos membros da família continuam destinadas à tela inicial (somente quando há atualizações relevantes) e à aba Família.
- As LDs permanecem na área própria de LDs recebidas/enviadas, não no feed de atualizações.

## Validação
- Verificação de sintaxe JavaScript executada para `app.js` e `index.js`.
- Não foi realizado deploy nem teste integrado contra Cloudflare/D1.

## Correções adicionais — atualizações dos membros lentas e notificações excluídas que voltavam
**Notificações excluídas voltavam**
- Causa 1 (corrida): a consulta periódica de notificações podia começar antes de excluir e terminar depois; a resposta antiga sobrescrevia a lista e o item reaparecia (o mesmo valia para "Dar ciência"). Agora respostas mais antigas que uma consulta já iniciada (ou de outra conta) são descartadas, e exclusão, exclusão definitiva, esvaziar lixeira e ciência passam a valer na hora no aparelho (marcadores locais por até 2 min, removidos assim que o servidor confirma). Em caso de erro do servidor, a ação é revertida e o erro é mostrado.
- Causa 2 (persistência): as notificações eram gravadas no localStorage e reapareciam ao reabrir o app até a primeira consulta. Agora vêm sempre do servidor e não são persistidas.
- Causa 3: no feed "Atualizações dos membros", o botão "Dar ciência" não retirava o item da lista. Agora o item com ciência sai do feed.
- O servidor já fazia a exclusão corretamente (`deleted_at`); só foi adicionado `Cache-Control: no-store, private` na listagem.

**Atualizações dos membros demoravam a aparecer**
- Consulta periódica de 15 s para 8 s e atualização imediata ao voltar para a aba, ao focar a janela e ao recuperar a conexão (abas em segundo plano têm o timer limitado pelo navegador).
- A tela só é redesenhada quando algo muda (antes, Família era redesenhada a cada consulta).
- Mudança de data de uma notificação já existente também conta como alteração.
- Lado de quem altera: os avisos aos familiares passaram a ser enviados em paralelo e sem bloquear a fila de sincronização (antes, cada aviso esperava o anterior e atrasava a sincronização do módulo seguinte).
- Texto da notificação de tarefas mantido como na v120.

**Validação**: `node --check` em app.js e index.js; teste simulado com resposta atrasada (item excluído não reaparece). Falta testar com duas contas reais.
