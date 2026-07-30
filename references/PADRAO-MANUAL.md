# Padrão de Geração de Manual HTML para LISP

Este documento serve como **PROMPT PADRÃO** para gerar manuais HTML para plugins LISP. Sempre que for solicitado criar um manual para uma nova LISP, utilize a estrutura HTML e CSS abaixo como base imutável, alterando apenas o conteúdo de texto para se adequar ao plugin específico.

## Instruções para a IA

1.  **Analise a LISP**: Identifique Nome, Versão, Comando de Ativação, Modos de Uso e Descrição.
2.  **Mantenha o Visual**: Não altere o CSS, as cores (`neon`, `base`, `surface`, etc.) ou a estrutura dos scripts Tailwind/JS. O visual deve ser **exatamente** o mesmo do template.
3.  **Substitua o Conteúdo**: Preencha as seções do HTML abaixo com os dados da LISP analisada.

---

## Template HTML (Copiar e Preencher)

```html
<!DOCTYPE html>
<html lang="pt-br">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- SUBST: Título da Aba -->
    <title>{{NOME_DO_PLUGIN}} | Guia Visual</title>

    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link
        href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;900&family=JetBrains+Mono:wght@400;500;700&display=swap"
        rel="stylesheet">

    <script src="https://cdn.tailwindcss.com"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        // REGRAS DE CORES GLOBAIS
                        white: '#E1E4E8',      
                        
                        base: '#16191D',       
                        surface: '#21262B',    
                        surfaceHighlight: '#2A3036',
                        stroke: '#30363D',     
                        neon: '#D455FF',       
                        success: '#3FB950',    
                        warning: '#E3B341',    
                        error: '#F85149',      
                        txtHead: '#E1E4E8',    
                        txtBody: '#8B949E',    
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['JetBrains Mono', 'monospace'],
                    },
                    letterSpacing: {
                        tightest: '-0.04em',
                        tight: '-0.02em',   
                    },
                    boxShadow: {
                        'glow': '0 0 20px rgba(212, 85, 255, 0.15), 0 0 40px rgba(212, 85, 255, 0.05)',
                    },
                    animation: {
                        'pulse-slow': 'pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite',
                        'cursor': 'cursor .75s step-end infinite',
                    },
                    keyframes: {
                        cursor: {
                            '0%, 100%': { opacity: '1' },
                            '50%': { opacity: '0' },
                        }
                    }
                }
            }
        }
    </script>

    <style>
        body { background-color: #16191D; color: #E1E4E8; scroll-behavior: smooth; }
        .bg-void {
            background: radial-gradient(circle at 50% 0%, rgba(212, 85, 255, 0.06) 0%, transparent 70%);
            position: fixed; inset: 0; z-index: 0; pointer-events: none;
        }
        ::selection { background: #D455FF; color: #E1E4E8; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
        details > summary { list-style: none; }
        details > summary::-webkit-details-marker { display: none; }
    </style>
</head>

<body class="font-sans antialiased min-h-screen pb-32 relative">

    <div class="bg-void"></div>

    <!-- TOAST NOTIFICATION -->
    <div id="toast" class="fixed top-24 right-6 bg-success text-white px-4 py-3 rounded shadow-glow transform translate-x-64 transition-transform duration-300 z-50 flex items-center gap-3 opacity-0">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path></svg>
        <span class="font-mono uppercase text-sm font-bold">Comando copiado!</span>
    </div>

    <nav class="fixed top-0 left-0 w-full z-40 bg-base/90 backdrop-blur border-b border-stroke py-2 px-3 md:px-12 flex justify-between items-center">
        <div class="flex items-center gap-2 select-none">
            <div class="font-black text-white text-3xl tracking-tightest">
                B<span class="text-neon">.</span>
            </div>
            <!-- SUBST: Nome curto do plugin (FIXO: Bali.CAD) -->
            <span class="font-mono uppercase text-xl text-txtBody opacity-80 tracking-widest border-l border-stroke pl-4 ml-3">
                Bali<span class="text-neon">.CAD</span>
            </span>
        </div>
        <div class="hidden md:flex gap-6 text-xs font-mono uppercase tracking-wider text-txtBody">
            <a href="#install" class="hover:text-neon transition">01. Instalar</a>
            <a href="#usage" class="hover:text-neon transition">02. Como Usar</a>
            <a href="#faq" class="hover:text-neon transition">03. Ajuda</a>
        </div>
    </nav>

    <main class="relative z-10 max-w-4xl mx-auto px-6 pt-32">

        <!-- HEADER HERO -->
        <header class="mb-20 text-center">
            <!-- SUBST: Tag de versão e nome -->
            <div class="inline-flex items-center gap-2 px-3 py-1 border border-neon/30 text-neon font-mono uppercase text-xs mb-8 rounded-full bg-neon/5 shadow-glow">
                <span class="relative flex h-2 w-2">
                  <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-neon opacity-75"></span>
                  <span class="relative inline-flex rounded-full h-2 w-2 bg-neon"></span>
                </span>
                {{NOME_TECNICO_PLUGIN}} v{{VERSAO}}
            </div>
            
            <h1 class="text-5xl md:text-7xl font-black text-white tracking-tightest mb-6 leading-[0.9]">
                MANUAL DE <br>
                <span class="text-neon">INSTALAÇÃO</span>
            </h1>
            
            <!-- SUBST: Breve descrição -->
            <p class="text-lg text-txtBody max-w-2xl mx-auto leading-relaxed font-light">
                {{DESCRICAO_CURTA}}
                <span class="text-neon font-medium">Leia com atenção.</span>
            </p>
        </header>

        <!-- SEÇÃO DE INSTALAÇÃO (PADRÃO PARA TODOS - NÃO MEXER MUITO) -->
        <section id="install" class="mb-32 scroll-mt-32">
            <div class="flex items-end justify-between border-b border-stroke pb-4 mb-10">
                <h2 class="text-2xl font-semibold tracking-tight text-white flex items-center gap-3">
                    <span class="bg-neon text-base w-8 h-8 rounded flex items-center justify-center text-sm font-bold text-black font-mono">01</span>
                    Instalação Passo-a-Passo
                </h2>
                <span class="text-xs font-mono uppercase text-txtBody hidden md:block tracking-wide">Tempo: 30 Seg</span>
            </div>

            <div class="space-y-8">
                <!-- STEP 1 -> APPLOAD -->
                <div class="bg-surface rounded-2xl border border-stroke overflow-hidden flex flex-col md:flex-row group hover:border-neon/30 transition duration-300">
                    <div class="p-8 md:w-1/2 flex flex-col justify-center border-b md:border-b-0 md:border-r border-stroke bg-surfaceHighlight/30">
                        <span class="text-neon font-mono uppercase text-xs mb-2 tracking-wider">Passo 01</span>
                        <h3 class="text-xl font-semibold tracking-tight text-white mb-4">Abrir Carregador</h3>
                        <p class="text-sm text-txtBody mb-4">No ZWCAD, digite o comando universal abaixo e aperte <kbd class="bg-stroke px-2 py-0.5 rounded text-white font-mono text-xs border border-white/10">ENTER</kbd>.</p>
                        <button onclick="copyToClipboard('_APPLOAD')" class="bg-base hover:bg-black border border-neon/50 text-neon font-mono uppercase py-4 px-6 rounded flex justify-between items-center group-hover:shadow-glow transition cursor-pointer relative overflow-hidden">
                            <span class="font-bold text-lg tracking-tight">_APPLOAD</span>
                            <div class="flex items-center gap-2 text-xs opacity-50"><span>COPIAR</span></div>
                        </button>
                    </div>
                    <div class="md:w-1/2 bg-black relative flex items-center justify-center p-8">
                        <div class="w-full font-mono text-sm bg-base border border-stroke rounded p-4 shadow-2xl opacity-80 group-hover:opacity-100 transition">
                            <div class="text-txtBody text-xs border-b border-stroke pb-2 mb-2 flex justify-between uppercase">
                                <span>Linha de Comando</span><span class="text-[10px]">x</span>
                            </div>
                            <div class="space-y-1">
                                <div class="text-txtBody">Command: <span class="text-neon font-bold">_APPLOAD</span><span class="animate-cursor bg-neon w-2 h-4 inline-block align-middle ml-1"></span></div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- STEP 2 -> STARTUP SUITE -->
                <div class="bg-surface rounded-2xl border border-stroke overflow-hidden flex flex-col md:flex-row group hover:border-neon/30 transition duration-300">
                    <div class="p-8 md:w-1/2 flex flex-col justify-center border-b md:border-b-0 md:border-r border-stroke bg-surfaceHighlight/30">
                        <span class="text-neon font-mono uppercase text-xs mb-2 tracking-wider">Passo 02</span>
                        <h3 class="text-xl font-semibold tracking-tight text-white mb-4">Inicialização Automática</h3>
                        <p class="text-sm text-txtBody mb-4">Na janela que abrir, procure pelo ícone de uma <strong>Maleta</strong> ou botão "Conteúdo" (Startup Suite).</p>
                        <div class="bg-warning/10 border border-warning/20 p-3 rounded">
                            <p class="text-warning text-xs flex gap-2 items-start">
                                <span><strong>Importante:</strong> Se não colocar aqui, o plugin vai sumir quando fechar o ZWCAD.</span>
                            </p>
                        </div>
                    </div>
                    <!-- Icone da Maleta Visual -->
                    <div class="md:w-1/2 bg-base relative flex items-center justify-center p-8">
                        <div class="flex flex-col items-center gap-4 text-txtBody group-hover:text-white transition">
                            <svg class="w-12 h-12 text-neon" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6"></path></svg>
                            <span class="font-mono text-xs uppercase tracking-widest text-center">Conteúdo<br>(Startup Suite)</span>
                        </div>
                    </div>
                </div>

                <!-- STEP 3 -> SELECT FILE -->
                <div class="bg-surface rounded-2xl border border-stroke overflow-hidden flex flex-col md:flex-row group hover:border-neon/30 transition duration-300">
                     <div class="p-8 md:w-1/2 flex flex-col justify-center border-b md:border-b-0 md:border-r border-stroke bg-surfaceHighlight/30">
                        <span class="text-neon font-mono uppercase text-xs mb-2 tracking-wider">Passo 03</span>
                        <h3 class="text-xl font-semibold tracking-tight text-white mb-4">Carregar Arquivo</h3>
                        <p class="text-sm text-txtBody mb-4">Clique em <strong>ADICIONAR (ADD)</strong> e selecione o arquivo <code>.zelx</code> correspondente.</p>
                    </div>
                    <div class="md:w-1/2 bg-base relative flex items-center justify-center p-8">
                        <!-- SUBST: Nome do arquivo no Card -->
                        <div class="relative w-32 h-40 bg-surface border border-neon rounded-lg shadow-glow flex flex-col items-center justify-center p-4">
                            <div class="text-neon font-mono font-bold text-3xl mb-2">L</div>
                            <div class="w-full h-px bg-stroke mb-2"></div>
                            <span class="text-[10px] text-txtBody font-mono uppercase break-all text-center">{{NOME_ARQUIVO}}</span>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- SEÇÃO DE USO (PERSONALIZAR AQUI) -->
        <section id="usage" class="mb-32 scroll-mt-32">
            <div class="flex items-end justify-between border-b border-stroke pb-4 mb-10">
                <h2 class="text-2xl font-semibold tracking-tight text-white flex items-center gap-3">
                    <span class="bg-neon text-base w-8 h-8 rounded flex items-center justify-center text-sm font-bold text-black font-mono">02</span>
                    Como Usar
                </h2>
            </div>

            <div class="grid md:grid-cols-2 gap-8">
                <!-- CARD COMANDO -->
                <div class="bg-surface p-8 rounded-2xl border border-stroke text-center group hover:border-neon transition flex flex-col items-center justify-center">
                    <div class="w-16 h-16 bg-base rounded-full border border-stroke flex items-center justify-center mb-6 group-hover:scale-110 transition group-hover:border-neon group-hover:shadow-glow text-2xl">
                        ⌨️
                    </div>
                    <h3 class="text-lg font-semibold tracking-tight text-white mb-2">Digite o Atalho</h3>
                    <p class="text-sm text-txtBody mb-6">Dentro do ZWCAD, digite o comando para iniciar.</p>
                    <!-- SUBST: Comando Principal -->
                    <button onclick="copyToClipboard('{{COMANDO_PRINCIPAL}}')" class="bg-black border border-stroke text-neon font-mono uppercase py-2 px-6 rounded hover:bg-neon/10 transition flex items-center gap-2 text-sm font-bold">
                        {{COMANDO_PRINCIPAL}}
                        <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7v8a2 2 0 002 2h6M8 7V5a2 2 0 012-2h4.586a1 1 0 01.707.293l4.414 4.414a1 1 0 01.293.707V15a2 2 0 01-2 2h-2M8 7H6a2 2 0 01-2-2V5"></path></svg>
                    </button>
                </div>

                <!-- CARD DE FUNCIONALIDADE (Exemplificar Modos) -->
                <div class="bg-surface p-8 rounded-2xl border border-stroke text-left group hover:border-neon transition">
                    <div class="w-16 h-16 bg-base rounded-full border border-stroke flex items-center justify-center mb-6 group-hover:scale-110 transition group-hover:border-neon group-hover:shadow-glow text-2xl mx-auto">
                        ⚙️
                    </div>
                    <h3 class="text-lg font-semibold tracking-tight text-white mb-4 text-center">Modos Disponíveis</h3>
                    <!-- SUBST: Lista de Modos ou Funcionalidades -->
                    <ul class="text-sm text-txtBody space-y-2 font-mono">
                         <!-- INSERIR LISTA DE MODOS AQUI -->
                         <!-- Exemplo: <li class="flex gap-2"><span class="text-neon">></span> Modo 1: Descrição</li> -->
                         {{LISTA_DE_MODOS_HTML}}
                    </ul>
                </div>
            </div>
        </section>

        <!-- TROUBLESHOOTING (FAQ) -->
        <section id="faq" class="mb-24">
            <h2 class="text-lg font-semibold tracking-tight text-white mb-6 flex items-center gap-3 border-l-4 border-neon pl-4">
                NÃO FUNCIONOU?
                <span class="text-xs font-mono uppercase text-txtBody opacity-50">(FAQ)</span>
            </h2>
            <div class="grid gap-4">
                <details class="group bg-surface border border-stroke rounded-lg overflow-hidden">
                    <summary class="flex justify-between items-center p-4 cursor-pointer hover:bg-white/5 select-none">
                        <!-- SUBST: Erro Comum 1 -->
                        <span class="font-medium text-white text-sm">O comando "{{COMANDO_PRINCIPAL}}" não existe</span>
                        <svg class="w-5 h-5 text-txtBody group-open:rotate-180 transition-transform" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg>
                    </summary>
                    <div class="p-4 pt-0 text-txtBody text-sm border-t border-stroke mt-2 bg-base/50">
                        <p class="mt-4">Isso significa que o arquivo não foi carregado corretamente. Verifique se adicionou ao <strong>Startup Suite</strong> e reinicie o ZWCAD.</p>
                    </div>
                </details>
            </div>
        </section>

        <footer class="border-t border-stroke pt-12 text-center pb-12">
            <p class="font-mono text-[10px] text-txtBody opacity-50 tracking-widest uppercase mb-2">
                Bali.CAD - Sistemas de Automação
            </p>
        </footer>

    </main>

    <script>
        function copyToClipboard(text) {
            navigator.clipboard.writeText(text).then(() => {
                const toast = document.getElementById('toast');
                toast.classList.remove('translate-x-64', 'opacity-0');
                setTimeout(() => toast.classList.add('translate-x-64', 'opacity-0'), 3000);
            }).catch(err => {
                alert("Copie manualmente: " + text);
            });
        }
    </script>
</body>
</html>
```
