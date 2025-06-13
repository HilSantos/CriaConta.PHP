# CriaConta.PHP

<?php
// Inclui arquivos de segurança, cabeçalho e conexão com o banco de dados //
include('segurancazero.php');
include('cabecalho.php');
include('conn.php');

// Verifica se o formulário foi enviado via método POST //
if($_SERVER['REQUEST_METHOD']=='POST'){

    // Captura os dados enviados pelo formulário //
    $nome = $_POST['nome'];
    $apelido = $_POST['apelido'];
    $cpf = $_POST['cpf'];
    $email = $_POST['email'];
    $cep = $_POST['cep'];
    $rua = $_POST['rua'];
    $numero = $_POST['numero'];
    $bairro = $_POST['bairro'];
    $cidade = $_POST['cidade'];
    $uf = $_POST['uf'];
    $nascimento = $_POST['dt_nascimento'];
    $telefone = $_POST['telefone'];
    $nivel = 1; // Define o nível de acesso do usuário (1 = cliente) //
    $senha = $_POST['senha'];
    $senha2 = $_POST['senha2'];

    //verifique se as senhas são iguais//
    if($senha!=$senha2){
        header('Location:cadastracliente.php?msg=As senhas devem ser iguais');
        exit();
    }

    // Gera um "tempero" (salt) para a senha, combinando número aleatório, data e senha invertida //
    $tempero = rand(100000000,999999999) . date("Y-m-d H:i:s") . strrev($senha);
    $tempero = md5($tempero); // Aplica hash MD5 ao tempero //
    // Verifica se as senhas digitadas são iguais //
    $senha = md5($senha . $tempero); // Aplica hash MD5 à senha concatenada com o tempero //

    // Verifica se o e-mail já está cadastrado no banco de dados //
    $sql = "SELECT COUNT(*) FROM tb_usuarios WHERE email_usuario = '$email'";
    $result = mysqli_query($link, $sql);
    $quant = mysqli_fetch_array($result);
    mysqli_close($link);

    // Se o e-mail já estiver cadastrado, redireciona com mensagem de erro //
    if($quant[0] == 1){
        header('Location: cadastracliente.php?msg=Email já cadastrado');
        exit();
    }

    // Monta a query para inserir o novo usuário no banco de dados //
    $sql = "INSERT INTO `tb_usuarios`(`nome_usuario`, `apelido_usuario`, `cpf_usuario`, `email_usuario`, `cep_usuario`, 
    `rua_usuario`, `numero_rua_usuario`, `bairro_usuario`, `cidade_usuario`, `uf_usuario`, `nascimento_usuario`, `senha_usuario`, 
    `telefone_usuario`, `nivel_usuario`, tempero_usuario) VALUES (
        '$nome','$apelido','$cpf','$email','$cep','$rua','$numero','$bairro','$cidade','$uf','$nascimento','$senha',
        '$telefone','$nivel','$tempero')";

    // Executa a inserção no banco //
    mysqli_query($link, $sql);

    // Fecha a conexão com o banco //
    mysqli_close($link);

    // Redireciona para a tela de login com mensagem de sucesso //
    header('location: login.php?msg=Conta criada, faça seu login.');
    exit();
?>

<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="cadastra.css">
    <title>Cadastra Usuario</title>
</head>
<body>
    <h1>Cadastra Usuario</h1>
    <br>
<?php
include('msg_user.php');
?>
    <form action="criaconta.php" method="post">
        <label for="nome">Nome:</label>
        <input type="text" name="nome" id="nome" maxlength="50" required>
        <br>
        <label for="apelido">Alias</label>
        <input type="text" name="apelido" id="alias" maxlength="15" required>
        <br>
        <label for="cpf">CPF:</label>
        <input type="text" name="cpf" id="cpf" maxlength="14" required>
        <br>
        <label for="email">Email:</label>
        <input type="email" name="email" id="email" maxlength="50" required>
        <br>
        <label for="cep">CEP:</label>
        <input type="text" name="cep" id="cep" maxlength="9" required>
        <br>
        <label for="rua">Logradouro:</label>
        <input type="text" name="rua" id="rua" maxlength="50" required>
        <br>
        <label for="numero">Numero:</label>
        <input type="text" name="numero" id="numero" maxlength="5" required>
        <br>
        <label for="bairro">Bairro:</label>
        <input type="text" name="bairro" id="bairoo" maxlength="30" required>
        <br>
        <label for="cidade">Cidade:</label>
        <input type="text" name="cidade" id="cidade" maxlength="30" required>
        <br>
        <label for="uf">UF:</label>
        <input type="text" name="uf" id="uf" maxlength="2" required>
        <br>
        <label for="dt_nascimento">Data de Nascimento:</label>
        <input type="date" name="dt_nascimento" id="dt_nascimento" required>
        <br>
        <label for="telefone">Telefone:</label>
        <input type="text" name="telefone" id="telefone" maxlength="11" required>
        <br>
        <label for="senha">Senha:</label>
        <input type="password" name="senha" id="senha" required>
        <br>
        <label for="senha2">Repita a senha</label>
        <input type="password" name="senha2" id="senha2" required>
        <br>
        <br>
        <input type="submit" value="enviar">
    </form>
</body>
</html>
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.mask/1.14.16/jquery.mask.min.js"></script>
<script>
$(document).ready(function() {
    // Máscara para o campo CPF //
    $('#cpf').mask('000.000.000-00', {reverse: true});
    // Máscara para o campo CEP //
    $('#cep').mask('00000-000');
    // Máscara para o campo Telefone //
    $('#telefone').mask('(00) 00000-0000', {placeholder: "(XX) XXXXX-XXXX"});
    // Máscara para o campo Número //
    $('#numero').mask('00000', {placeholder: "00000"});
    // Máscara para o campo UF //
    $('#uf').mask('AA', {placeholder: "XX"});
    // Máscara para o campo Data de Nascimento //
    $('#dt_nascimento').mask('00/00/0000', {placeholder: "DD/MM/AAAA"});
    // Máscara para o campo Apelido //
    $('#alias').mask('AAAAAAAAAAAAAAA', {placeholder: "Apelido"});
    // Máscara para o campo Rua //
    $('#rua').mask('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA', {placeholder: "Logradouro"});
    // Máscara para o campo Bairro //
    $('#bairoo').mask('AAAAAAAAAAAAAAAAAAAAAA', {placeholder: "Bairro"});
    // Máscara para o campo Cidade //
    $('#cidade').mask('AAAAAAAAAAAAAAAAAAAAAA', {placeholder: "Cidade"});
    // Máscara para o campo Email //
    $('#email').mask('A', {
        translation: {
            'A': { pattern: /[\w@\-.+]/, recursive: true }
        },
        placeholder: "Email"
    });
});
// Script para buscar o endereço pelo CEP usando a API ViaCEP //
document.addEventListener("DOMContentLoaded", function() {
            const cepInput = document.getElementById("cep");
 
cepInput.addEventListener("blur", function() {
                let cep = cepInput.value.replace(/\D/g, ''); // Remove tudo que não é número //
 
if (cep.length === 8) { // Valida se são 8 dígitos //
                    // Faz a requisição para a API ViaCEP //
                    fetch(`https://viacep.com.br/ws/${cep}/json/`)
                        .then(response => {
                            if (!response.ok) {
                                throw new Error('Erro ao buscar o CEP');
                            }
                            return response.json();
                        })
                        .then(data => {
                            if (data.erro) {
                                alert("CEP não encontrado.");
                                return;
                            }
                            // Preenche os campos do formulário //
                            document.getElementById("rua").value = data.logradouro;
                            document.getElementById("bairro").value = data.bairro;
                            document.getElementById("cidade").value = data.localidade;
                            document.getElementById("uf").value = data.uf;
                        })
                        .catch(error => {
                            console.error("Erro na busca do CEP: ", error);
                            alert("Não foi possível buscar o endereço.");
                        });
                } else {
                    alert("Formato de CEP inválido. Deve conter 8 dígitos numéricos.");
                }
            });
        });
</script>
<?php
} else {
    // Se o formulário não foi enviado, redireciona para a página de cadastro //
    header('Location: cadastracliente.php');
    exit();
}
// Fim do script criaconta.php //
?>
