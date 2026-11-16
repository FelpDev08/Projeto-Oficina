# GARAGEM 617 — Site completo

Site em HTML/CSS/JS puro (sem build), pronto para publicar na Vercel. Banco de dados, login do painel e upload de fotos usam o **Supabase** (grátis).

## 1) Testar agora (modo demonstração)
Abra `index.html` no navegador ou publique na Vercel. Sem configurar nada, tudo funciona, mas os dados ficam **só no navegador** de quem acessa.
Painel: `/admin` — e-mail `admin@garagem617.demo`, senha `admin617` (só funciona no modo demonstração).

## 2) Publicar na Vercel
Pré-requisitos: conta grátis em https://vercel.com e Node.js (versão LTS) instalado.
1. Abra o terminal **dentro da pasta que contém o `index.html`** (confira com `dir` no Windows ou `ls` no Mac/Linux).
2. Na primeira vez, faça login: `npx vercel login`.
3. Publique: `npx vercel --prod`.
   - Na pergunta *Which project?* escolha **Create a new project** (primeira vez) ou o projeto já existente.
   - O nome do projeto deve ser **minúsculo** (ex.: `garagem617`).
   - Nas demais perguntas, aperte Enter.
4. No final aparece `Production: https://seu-projeto.vercel.app`. Esse link é público.
5. Para atualizar o site depois de qualquer mudança, rode `npx vercel --prod` de novo na mesma pasta.
6. O endereço `https://garagem617.vercel.app` já está em `index.html`, `sitemap.xml` e `robots.txt`. Se o seu link for outro, troque nesses 3 arquivos.

O arquivo `vercel.json` já traz os redirecionamentos e cabeçalhos de segurança. Se a Vercel pedir login para abrir o site, desative em *Settings → Deployment Protection*.

## 3) Ligar o banco de dados real (Supabase) — necessário para uso de verdade
1. Crie um projeto em https://supabase.com
2. **SQL Editor** → cole o conteúdo de `supabase/schema.sql` → **Run**.
3. **Authentication → Users → Add user**: crie seu e-mail e senha de administrador (marque *Auto Confirm*).
4. No SQL Editor rode (trocando o e-mail):
   ```sql
   insert into admin_users (id, name, email)
   select id, 'Administrador', email from auth.users where email = 'SEU-EMAIL@exemplo.com';
   ```
5. **Project Settings → API**: copie *Project URL* e a chave *anon public* para `assets/js/config.js`.
6. Publique de novo (`npx vercel --prod`). Entre em `/admin` com o usuário do passo 3.

A chave `anon` pode ficar no site: o acesso é protegido por regras (RLS) no banco. O público só consegue ler conteúdo do site, ver horários ocupados e **enviar** agendamentos/orçamentos; ler dados de clientes e fotos enviadas exige login de administrador.

### Fotos do cliente e WhatsApp
O link `wa.me` só leva texto, não anexa imagens. Por isso, na tela final do agendamento/orçamento, o botão do WhatsApp se comporta assim:
- **Celular:** abre o menu de compartilhar com as fotos e a mensagem juntas. O cliente escolhe o WhatsApp e o contato da Garagem 617.
- **Computador:** abre a conversa com a mensagem pronta e avisa para anexar as fotos pelo clipe 📎.

Com o Supabase ligado, as fotos também ficam salvas no painel `/admin`, junto com o pedido.

## 4) Trocar os placeholders
Tudo isto é editável em **/admin → Site e contato** (sem mexer em código): telefone, WhatsApp (somente números com 55+DDD), e-mail, endereço, horário, redes sociais, textos, números do “Sobre”, foto do topo e imagens da oficina.
Itens marcados `[SUBSTITUIR]` / `[EXEMPLO]` são provisórios: telefone, WhatsApp, e-mail, endereço, links de Instagram/Facebook/TikTok, os 6 trabalhos de exemplo e as 3 avaliações de exemplo.

## 5) Fotos
O projeto **não inclui fotos de carros**: onde não há foto real aparece uma imagem provisória com o aviso “substituir pela foto real”. Suba as fotos pelo painel (Trabalhos, Site e contato). Não use fotos de terceiros sem licença.

## Horários e dias de atendimento
Em `assets/js/config.js`: `SLOTS` (horários oferecidos) e `CLOSED_WEEKDAYS` (0 = domingo). Se mudar os horários, ajuste também a lista na função `create_booking` do `schema.sql`.

## Estrutura
```
index.html            site público
admin/index.html      painel (/admin)
assets/css            style.css, admin.css
assets/js             config.js, data.js (banco), site.js, admin.js
assets/img/site       hero, fotos do "Sobre" e imagem de compartilhamento (og)
assets/img/obras      fotos dos trabalhos (antes/depois etc.)
assets/img/avatares   fotos das avaliações
supabase/schema.sql   tabelas, segurança e armazenamento
vercel.json · robots.txt · sitemap.xml
```
