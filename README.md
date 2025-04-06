<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerador de modelo de tasks de desenvolvimento</title>
    <style>
        textarea, input, select {
        width: 100%;  /* Agora todos os campos terão a mesma largura */
        padding: 12px;
        margin-top: 8px;
        border-radius: 5px;
        border: 1px solid #ccc;
        font-size: 16px;
        box-sizing: border-box; /* Evita que o padding afete a largura */
    }
        body {
            font-family: Arial, sans-serif;
            background-color: #eef1f7;
            margin: 0;
            padding: 0;
        }
        .topo {
            background: #007bff;
            color: white;
            text-align: center;
            padding: 15px;
            font-size: 20px;
            font-weight: bold;
            position: relative;
        }
        .rodape {
                background: #007bff; /* Cor igual ao topo */
                height: 50px;
                width: 100%;
                position: relative; /* Mantém no fluxo da página */
                display: none; /* Inicialmente oculto */
                margin-top: 20px; /* Pequeno espaço antes do rodapé */
            }
            .botao-download-container {
                display: flex; /* Exibe os botões lado a lado */
                gap: 10px;     /* Espaçamento entre os botões */
                justify-content: center; /* Alinha os botões ao centro */
                margin-top: 20px;  /* Espaço entre o conteúdo anterior e os botões */
            }

            .botao-download-container button {
                width: 48%; /* Faz os botões ocupar metade da largura */
                padding: 15px;
                background: #007bff;
                color: white;
                font-size: 18px;
                border: none;
                border-radius: 5px;
                cursor: pointer;
            }

            .botao-download-container button:hover {
                background: #0056b3;
            }
        .btn-container {
            position: absolute;
            left: 10px;
            top: 50px;
            display: flex;
            gap: 10px;
        }
        .btn-voltar, .btn-limpar {
            background: #dc3545;
            color: white;
            border: none;
            font-size: 14px;
            padding: 8px 15px;
            border-radius: 5px;
            cursor: pointer;
            display: none;
            align-items: center;
        }
        .btn-voltar:hover { background: #c82333; }
        .btn-limpar { background: #007bff; }
        .btn-limpar:hover { background: #0056b3; }
        .container, .selecionar-tipo {
            max-width: 600px;
            background: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
            margin: 20px auto;
            text-align: center;
        }
        h2 { color: #333; }
        label { font-weight: bold; display: block; margin-top: 15px; color: #555; }
        textarea, input, select {
            width: 100%;
            padding: 12px;
            margin-top: 8px;
            border-radius: 5px;
            border: 1px solid #ccc;
            font-size: 16px;
        }
        button {
            width: 100%;
            background: #007bff;
            color: white;
            padding: 15px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 18px;
            margin-top: 20px;
        }
        button:hover { background: #0056b3; }
        pre {
            background: #282c34;
            color: #fff;
            padding: 20px;
            border-radius: 5px;
            font-size: 16px;
            overflow-x: auto;
            margin-top: 20px;
        }
    </style>
    <script>
        function selecionarTipo() {
            let tipo = document.getElementById("tipoTarefa").value;
            let tituloInput = document.getElementById("titulo");
            tituloInput.value = tipo + " - ";
            tituloInput.setAttribute("data-prefix", tipo + " - ");
            document.getElementById("selecionarTipo").style.display = "none";
            document.getElementById("formulario").style.display = "block";
            document.getElementById("btnVoltar").style.display = "inline-block";
            document.getElementById("btnLimpar").style.display = "inline-block";
            
            let campoEmail = document.getElementById("campoEmail");
            if (tipo === "Backup") {
                campoEmail.style.display = "block";
            } else {
                campoEmail.style.display = "none";
            }
        }
        
        function restringirTitulo(event) {
            let input = event.target;
            let prefixo = input.getAttribute("data-prefix");
            if (!input.value.startsWith(prefixo)) {
                input.value = prefixo;
            }
        }

        function voltarSelecao() {
            document.getElementById("formulario").style.display = "none";
            document.getElementById("selecionarTipo").style.display = "block";
            document.getElementById("btnVoltar").style.display = "none";
            document.getElementById("btnLimpar").style.display = "none";
        }

        function limparCampos() {
            document.getElementById("descricao").value = "";
            document.getElementById("simulacao").value = "";
            document.getElementById("cliente").value = "";
            document.getElementById("nivelAtencao").value = "Baixo";
            document.getElementById("emailBackup").value = "";
        }
    </script>
</head>


<script>
    window.addEventListener("scroll", function () {
        let rodape = document.querySelector(".rodape");
        if (window.scrollY > 100) { // Se descer mais de 100px - script de rolagem de tela
            rodape.style.display = "block";
        } else {
            rodape.style.display = "none";
        }
    });
</script>

<script>
    document.getElementById("emailBackup").addEventListener("input", function() {
        let email = this.value;
        let emailErro = document.getElementById("emailErro");
        let regex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.(com|com\.br|net|org|edu|gov|br)$/i; 

        if (!regex.test(email)) {
            emailErro.style.display = "block"; // Mostra a mensagem em vermelho - script de email errado
        } else {
            emailErro.style.display = "none"; // Esconde a mensagem se for válido
        }
    });
</script>

<script>
    function GerarDocumentoCtrlC() {
        let titulo = document.getElementById("titulo").value.trim();
        let descricao = document.getElementById("descricao").value;
        let simulacao = document.getElementById("simulacao").value;
        let cliente = document.getElementById("cliente").value;
        let nivelAtencao = document.getElementById("nivelAtencao").value;

        // Formata o nível de atenção corretamente com [X] antes da palavra
        let nivelFormatado = `[X] ${nivelAtencao}`;

        // Formata o texto corretamente
        let texto = `Titulo:\n${titulo}\n\nDescrição:\n${descricao}\n\nComo Simular a Tarefa:\n${simulacao}\n\nNome do Cliente:\n${cliente}\n\nNível de Atenção:\n${nivelFormatado}`;

        // Copia o texto para a área de transferência
        navigator.clipboard.writeText(texto).then(() => {
            alert("✅ Os dados do formulário foram copiados, agora você pode colar no bitrix.");
           
        }).catch(err => {
            alert("❌ Erro ao copiar o texto!");
            console.error("Erro ao copiar:", err);
        });
    }
</script>


<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script>
   function gerarTarefa() {
    let titulo = document.getElementById("titulo").value.trim();
    let descricao = document.getElementById("descricao").value;
    let simulacao = document.getElementById("simulacao").value;
    let cliente = document.getElementById("cliente").value;
    let nivelAtencao = document.getElementById("nivelAtencao").value;
    let emailBackup = document.getElementById("emailBackup").value;

    let tarefa = { titulo, descricao, simulacao, cliente, nivelAtencao, emailBackup };
    localStorage.setItem("tarefaSalva", JSON.stringify(tarefa));

    let conteudo = `📌 Título da Tarefa: ${titulo}\n\n` +
                   `📝 Descrição da Tarefa:\n${descricao}\n\n` +
                   `🔍 Como Simular a Tarefa:\n${simulacao}\n\n` +
                   `🧑‍💼 Nome do Cliente: ${cliente}\n\n` +
                   `⚠️ Nível de Atenção: ${nivelAtencao}\n\n`;

    if (document.getElementById("campoEmail").style.display === "block") {
        conteudo += `📧 E-mail para Backup: ${emailBackup}\n\n`;
    }

    // Redireciona para a página modelopronto.html
    window.location.href = "modelopronto.html";
}


    <script>
    
     <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>   
    <script>      
    function gerarPDF() {
        const { jsPDF } = window.jspdf;
        let titulo = document.getElementById("titulo").value.trim();
        let descricao = document.getElementById("descricao").value;
        let simulacao = document.getElementById("simulacao").value;
        let cliente = document.getElementById("cliente").value;
        let nivelAtencao = document.getElementById("nivelAtencao").value;
        let emailBackup = document.getElementById("emailBackup").value;

        // Substituir emojis por texto simples para PDF
        let tituloSemEmojis = titulo.replace("📌", "[Título]");
        let descricaoSemEmojis = descricao.replace("📝", "[Descrição]");
        let simulacaoSemEmojis = simulacao.replace("🔍", "[Simulação]");
        let clienteSemEmojis = cliente.replace("🧑‍💼", "[Cliente]");
        let nivelAtencaoSemEmojis = nivelAtencao.replace("⚠️", "[Nível de Atenção]");
        let emailBackupSemEmojis = emailBackup.replace("📧", "[E-mail]");

        // Criação do PDF
        let doc = new jsPDF();
        doc.setFont("helvetica", "bold");
        doc.setFontSize(16);
        doc.text("Modelo de Tarefa", 10, 15);

        doc.setFont("helvetica", "normal");
        doc.setFontSize(12);
        doc.text(`Título: ${tituloSemEmojis}`, 10, 30);
        doc.text(`Descrição:`, 10, 45);
        doc.text(descricaoSemEmojis, 10, 50, { maxWidth: 180 });

        doc.text(`Como Simular:`, 10, 70);
        doc.text(simulacaoSemEmojis, 10, 75, { maxWidth: 180 });

        doc.text(`Cliente: ${clienteSemEmojis}`, 10, 95);
        doc.text(`Nível de Atenção: ${nivelAtencaoSemEmojis}`, 10, 110);

        if (document.getElementById("campoEmail").style.display === "block") {
            doc.text(`E-mail para Backup: ${emailBackupSemEmojis}`, 10, 125);
        }

        let nomeArquivo = titulo.replace(/[^a-zA-Z0-9-_]/g, "_") || "modelo_tarefa";
        nomeArquivo += ".pdf";

        doc.save(nomeArquivo);  // Salva e faz o download do PDF
        alert(`📄 PDF salvo e baixado como "${nomeArquivo}"`);
    }
</script>

<script>
    function gerartxt() {
        // Captura os valores preenchidos
        let titulo = document.getElementById("titulo").value.trim(); // Remove espaços extras
        let descricao = document.getElementById("descricao").value;
        let simulacao = document.getElementById("simulacao").value;
        let cliente = document.getElementById("cliente").value;
        let nivelAtencao = document.getElementById("nivelAtencao").value;
        let emailBackup = document.getElementById("emailBackup").value;

        // Salva no LocalStorage
        let tarefa = { titulo, descricao, simulacao, cliente, nivelAtencao, emailBackup };
        localStorage.setItem("tarefaSalva", JSON.stringify(tarefa));

        // Monta o conteúdo do arquivo
        let conteudo = 
            `📌 Título da Tarefa: ${titulo}\n\n` +
            `📝 Descrição da Tarefa:\n${descricao}\n\n` +
            `🔍 Como Simular a Tarefa:\n${simulacao}\n\n` +
            `🧑‍💼 Nome do Cliente: ${cliente}\n\n` +
            `⚠️ Nível de Atenção: ${nivelAtencao}\n\n`;

        if (document.getElementById("campoEmail").style.display === "block") {
            conteudo += `📧 E-mail para Backup: ${emailBackup}\n\n`;
        }

        // Substitui espaços e caracteres inválidos no nome do arquivo
        let nomeArquivo = titulo.replace(/[^a-zA-Z0-9-_]/g, "_") || "modelo_tarefa";  
        nomeArquivo += ".txt";  

        // Cria o arquivo TXT automaticamente
        let blob = new Blob([conteudo], { type: "text/plain" });
        let link = document.createElement("a");
        link.href = URL.createObjectURL(blob);
        link.download = nomeArquivo;
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);

        // Mensagem de confirmação
        alert(`✅ Download de arquivo de texto iniciado com nome de "${nomeArquivo}"`);
    }

    // Função para carregar os dados salvos ao abrir a página
    function carregarTarefaSalva() {
        let tarefaSalva = localStorage.getItem("tarefaSalva");
        if (tarefaSalva) {
            let dados = JSON.parse(tarefaSalva);
            document.getElementById("titulo").value = dados.titulo;
            document.getElementById("descricao").value = dados.descricao;
            document.getElementById("simulacao").value = dados.simulacao;
            document.getElementById("cliente").value = dados.cliente;
            document.getElementById("nivelAtencao").value = dados.nivelAtencao;
            document.getElementById("emailBackup").value = dados.emailBackup;
        }
    }

    // Carrega os dados automaticamente ao abrir a página
    window.onload = carregarTarefaSalva;
</script>


<body>
    <div class="topo">
        Task Generator
        <div class="btn-container">
            <button class="btn-voltar" id="btnVoltar" onclick="voltarSelecao()">◀️ Voltar</button>
            <button class="btn-limpar" id="btnLimpar" onclick="limparCampos()">🗑️ Limpar</button>
        </div>
    </div>
    
    <div class="selecionar-tipo" id="selecionarTipo">
        <label style="text-align: left; display: block; font-weight: lighter;">📂 Selecione aqui o tipo de tarefa</label>
        <select id="tipoTarefa">
            <option value="" disabled selected></option>
            <option value="Sugestão">Sugestão</option>
            <option value="Erro">Erro</option>
            <option value="Ajuste">Ajuste</option>
            <option value="Backup">Backup</option>
            <option value="DNS">DNS</option>
        </select>
        <button onclick="selecionarTipo()">Continuar</button>
    </div>
    
    <div class="container" id="formulario" style="display: none;">
        <h2>Gerador de Tarefas para Desenvolvedores</h2>
        
        <label style="text-align: left; display: block; font-weight: lighter;">🏷️ Titulo da tarefa</label>
        <input type="text" id="titulo" value="" oninput="restringirTitulo(event)">
        
        <label style="text-align: left; display: block; font-weight: lighter;">📌 Descrição da tarefa:</label>
        <textarea id="descricao" placeholder="Descreva detalhadamente a sugestão/erro relatado pelo cliente"></textarea>
        
        <label style="text-align: left; display: block; font-weight: lighter;">🔍Como simular a tarefa:</label>
        <textarea id="simulacao" placeholder="Explique como simular esta sugestão ou oque ela faria no sistema"></textarea>
        
        <label style="text-align: left; display: block; font-weight: lighter;">🧑 Nome do cliente:</label>
        <input type="text" id="cliente" placeholder="Digite aqui nome do cliente, site e código da imobiliária">
        
        <label style="text-align: left; display: block; font-weight: lighter;">⚠️ Nível de atenção:</label>
        <select id="nivelAtencao">
            <option value="" disabled selected>Selecione o nível de atenção </option>
            <option value="Baixo">⬇️ Baixo</option>
            <option value="Médio">✅ Normal</option>
            <option value="Alto">⚠️ Alto</option>
            <option value="Alto">🔥 Altissímo</option>
        </select>
        
        <div id="campoEmail" style="display: none;">
            <label style="text-align: left; display: block; font-weight: lighter;">📩 Email para envio do backup:</label>
            <input type="email" id="emailBackup" placeholder="Digite o e-mail para envio" required>
            <small id="emailErro" style="color: red; display: none;">Este e-mail é inválido</small>
        </div>
        
        <div class="botao-download-container">
        <button onclick="gerartxt()">📄 Download TXT</button>
        <button onclick="gerarPDF()"> ⬇️ Download PDF</button>
        <button onclick="GerarDocumentoCtrlC()">🚀 Gerar Modelo Pronto</button>
</div>
    </div>
    <div class="rodape"></div>
</body>
</html>
