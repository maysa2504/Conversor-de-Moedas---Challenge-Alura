# Conversor-de-Moedas---Challenge-Alura
Conversor de moedas desenvolvido em Java
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.URI;
import java.util.Scanner;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class ConversorMoedas {
    private static final String API_URL = "https://v6.exchangerate-api.com/v6/YOUR_API_KEY/latest/";

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        boolean continuar = true;

        while (continuar) {
            System.out.println("=== Conversor de Moedas ===");
            System.out.println("Escolha uma opção de conversão:");
            System.out.println("1. USD para BRL");
            System.out.println("2. EUR para BRL");
            System.out.println("3. GBP para BRL");
            System.out.println("4. BRL para USD");
            System.out.println("5. BRL para EUR");
            System.out.println("6. BRL para GBP");
            System.out.println("7. Sair");
            System.out.print("Digite a sua escolha: ");
            int escolha = scanner.nextInt();

            if (escolha == 7) {
                continuar = false;
                System.out.println("Obrigado por usar o Conversor de Moedas!");
            } else {
                processarConversao(escolha, scanner);
            }
        }

        scanner.close();
    }

    private static void processarConversao(int escolha, Scanner scanner) {
        String baseMoeda = "";
        String destinoMoeda = "";

        switch (escolha) {
            case 1:
                baseMoeda = "USD";
                destinoMoeda = "BRL";
                break;
            case 2:
                baseMoeda = "EUR";
                destinoMoeda = "BRL";
                break;
            case 3:
                baseMoeda = "GBP";
                destinoMoeda = "BRL";
                break;
            case 4:
                baseMoeda = "BRL";
                destinoMoeda = "USD";
                break;
            case 5:
                baseMoeda = "BRL";
                destinoMoeda = "EUR";
                break;
            case 6:
                baseMoeda = "BRL";
                destinoMoeda = "GBP";
                break;
            default:
                System.out.println("Opção inválida.");
                return;
        }

        System.out.print("Digite o valor em " + baseMoeda + ": ");
        double valor = scanner.nextDouble();

        try {
            double taxa = obterTaxaDeCambio(baseMoeda, destinoMoeda);
            double resultado = valor * taxa;

            System.out.printf("Valor convertido: %.2f %s%n", resultado, destinoMoeda);
        } catch (Exception e) {
            System.out.println("Erro ao obter as taxas de câmbio. Verifique sua conexão com a internet.");
        }
    }

    private static double obterTaxaDeCambio(String base, String destino) throws Exception {
        String url = API_URL + base;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        JsonObject jsonResponse = JsonParser.parseString(response.body()).getAsJsonObject();

        if (!jsonResponse.has("conversion_rates")) {
            throw new Exception("Erro ao buscar taxas de câmbio.");
        }

        JsonObject taxas = jsonResponse.getAsJsonObject("conversion_rates");
        return taxas.get(destino).getAsDouble();
    }
}
