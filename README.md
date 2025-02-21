package com.ctg;

import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeParseException;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import java.util.Scanner;

public class GestaoCTG {

    private static List<Socio> socios = new ArrayList<>();
    private static List<Mensalidade> mensalidades = new ArrayList<>();
    private static int proximoIdSocio = 1;
    private static int proximoIdMensalidade = 1;
    private static Scanner scanner = new Scanner(System.in);
    private static DateTimeFormatter dateFormatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");

    public static void main(String[] args) {
        while (true) {
            mostrarMenu();
            int opcao = obterOpcao();
            processarOpcao(opcao);
        }
    }

    private static void mostrarMenu() {
        System.out.println("Menu:");
        System.out.println("1. Cadastrar Sócio");
        System.out.println("2. Registrar Mensalidade");
        System.out.println("3. Listar Sócios");
        System.out.println("4. Listar Mensalidades");
        System.out.println("5. Sair");
        System.out.print("Escolha uma opção: ");
    }

    private static int obterOpcao() {
        while (!scanner.hasNextInt()) {
            System.out.println("Opção inválida. Tente novamente.");
            scanner.next(); // Descarta a entrada inválida
        }
        return scanner.nextInt();
    }

    private static void processarOpcao(int opcao) {
        scanner.nextLine(); // Consumir a quebra de linha
        switch (opcao) {
            case 1:
                cadastrarSocio();
                break;
            case 2:
                registrarMensalidade();
                break;
            case 3:
                listarSocios();
                break;
            case 4:
                listarMensalidades();
                break;
            case 5:
                System.out.println("Saindo...");
                scanner.close();
                System.exit(0);
                break;
            default:
                System.out.println("Opção inválida. Tente novamente.");
                break;
        }
    }

    private static void cadastrarSocio() {
        System.out.print("Nome do sócio: ");
        String nome = scanner.nextLine();
        System.out.print("Data de nascimento (dd/MM/yyyy): ");
        String dataNascimentoStr = scanner.nextLine();
        System.out.print("Contato: ");
        String contato = scanner.nextLine();

        if (nome.isEmpty() || dataNascimentoStr.isEmpty() || contato.isEmpty()) {
            System.out.println("Todos os campos são obrigatórios.");
            return;
        }

        LocalDate dataNascimento = parseDate(dataNascimentoStr);
        if (dataNascimento == null) return;

        Socio socio = new Socio(proximoIdSocio++, nome, dataNascimento, contato);
        socios.add(socio);
        System.out.println("Sócio cadastrado com sucesso.");
    }

    private static LocalDate parseDate(String data) {
        try {
            return LocalDate.parse(data, dateFormatter);
        } catch (DateTimeParseException e) {
            System.out.println("Data inválida. Use o formato dd/MM/yyyy.");
            return null;
        }
    }

    private static void registrarMensalidade() {
        System.out.print("ID do sócio: ");
        int idSocio = scanner.nextInt();
        scanner.nextLine(); // Consumir a quebra de linha

        Optional<Socio> socioOpt = socios.stream()
                .filter(s -> s.getId() == idSocio)
                .findFirst();

        if (socioOpt.isEmpty()) {
            System.out.println("Sócio não encontrado.");
            return;
        }

        System.out.print("Valor da mensalidade: ");
        double valor = scanner.nextDouble();
        scanner.nextLine(); // Consumir a quebra de linha

        if (valor <= 0) {
            System.out.println("O valor da mensalidade deve ser positivo.");
            return;
        }

        System.out.print("Data de pagamento (dd/MM/yyyy): ");
        String dataPagamentoStr = scanner.nextLine();
        LocalDate dataPagamento = parseDate(dataPagamentoStr);
        if (dataPagamento == null) return;

        Mensalidade mensalidade = new Mensalidade(proximoIdMensalidade++, valor, dataPagamento, false);
        mensalidades.add(mensalidade);
        System.out.println("Mensalidade registrada com sucesso.");
    }

    private static void listarSocios() {
        System.out.println("Lista de Sócios:");
        if (socios.isEmpty()) {
            System.out.println("Nenhum sócio cadastrado.");
        } else {
            for (Socio s : socios) {
                System.out.println(s);
            }
        }
    }

    private static void listarMensalidades() {
        System.out.println("Lista de Mensalidades:");
        if (mensalidades.isEmpty()) {
            System.out.println("Nenhuma mensalidade registrada.");
        } else {
            for (Mensalidade m : mensalidades) {
                System.out.println(m);
            }
        }
    }
}
