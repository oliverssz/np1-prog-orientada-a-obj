using System;
using System.Collections.Generic;
using System.Linq;

// Cliente
public class Cliente
{
    public string Nome { get; set; }
    public string CPF { get; set; }
    public string RG { get; set; }
    public Endereco Endereco { get; set; }
    public FaixaEtaria Idade { get; set; }
    public Sexo Sexo { get; set; }
    private List<Bilhete> Bilhetes { get; set; } = new List<Bilhete>();

    public Cliente(string nome, string cpf, string rg, Endereco endereco, FaixaEtaria idade, Sexo sexo)
    {
        Nome = nome;
        CPF = cpf;
        RG = rg;
        Endereco = endereco;
        Idade = idade;
        Sexo = sexo;
    }

    public void AdicionarBilhete(Bilhete bilhete)
    {
        Bilhetes.Add(bilhete);
        Console.WriteLine($"Bilhete adicionado para {Nome}:\n{bilhete}");
    }

    public void EmbarcarNoOnibus(Onibus onibus)
    {
        Console.WriteLine($"{Nome} embarcou no ônibus número {onibus.Numero} com destino {onibus.Itinerario}.");
    }

    public void ListarBilhetes()
    {
        Console.WriteLine($"Bilhetes de {Nome}:");
        foreach (var bilhete in Bilhetes)
        {
            Console.WriteLine(bilhete);
        }
    }
}

// Endereço
public class Endereco
{
    public string Rua { get; set; }
    public int Numero { get; set; }
    public string Cidade { get; set; }
    public string Estado { get; set; }
    public string CEP { get; set; }

    public Endereco(string rua, int numero, string cidade, string estado, string cep)
    {
        Rua = rua;
        Numero = numero;
        Cidade = cidade;
        Estado = estado;
        CEP = cep;
    }

    public void ExibirEndereco()
    {
        Console.WriteLine($"Endereço: {Rua}, {Numero} - {Cidade}, {Estado}, {CEP}");
    }
}

// Enum FaixaEtaria
public enum FaixaEtaria
{
    Crianca,
    Adulto,
    Idoso
}

// Enum Sexo
public enum Sexo
{
    Masculino,
    Feminino
}

// Bilhete
public class Bilhete
{
    public decimal Valor { get; set; }
    public DateTime Horario { get; set; }
    public Onibus Onibus { get; set; }
    public string CidadeInicial { get; set; }
    public string CidadeFinal { get; set; }

    public Bilhete(decimal valor, DateTime horario, Onibus onibus, string cidadeInicial, string cidadeFinal)
    {
        Valor = valor;
        Horario = horario;
        Onibus = onibus;
        CidadeInicial = cidadeInicial;
        CidadeFinal = cidadeFinal;
    }

    public override string ToString()
    {
        return $"Valor: {Valor:C}, Horário: {Horario}, Ônibus: {Onibus.Numero}, Cidade Inicial: {CidadeInicial}, Cidade Final: {CidadeFinal}";
    }
}

// Onibus
public class Onibus
{
    public int Numero { get; set; }
    public string Placa { get; set; }
    public int QuantidadeAssentos { get; set; }
    public string Itinerario { get; set; }
    private List<Cliente> passageiros = new List<Cliente>();

    public Onibus(int numero, string placa, int quantidadeAssentos, string itinerario)
    {
        Numero = numero;
        Placa = placa;
        QuantidadeAssentos = quantidadeAssentos;
        Itinerario = itinerario;
    }

    public void AdicionarPassageiro(Cliente cliente)
    {
        if (passageiros.Count < QuantidadeAssentos)
        {
            passageiros.Add(cliente);
            Console.WriteLine($"Cliente {cliente.Nome} embarcou no ônibus {Numero}.");
        }
        else
        {
            Console.WriteLine("Ônibus cheio. Não é possível adicionar mais passageiros.");
        }
    }

    public void ListarPassageiros()
    {
        Console.WriteLine($"Passageiros do ônibus {Numero}:");
        foreach (var passageiro in passageiros)
        {
            Console.WriteLine(passageiro.Nome);
        }
    }

    public void ExibirOnibus()
    {
        Console.WriteLine($"Ônibus: Número {Numero}, Placa {Placa}, Assentos {QuantidadeAssentos}, Itinerário {Itinerario}");
    }

    public List<Cliente> GetPassageiros()
    {
        return passageiros;
    }
}

// Sistema de Bilhetagem
public class SistemaBilhetagem
{
    private List<Cliente> clientes = new List<Cliente>();
    private List<Onibus> onibus = new List<Onibus>();
    private List<Bilhete> bilhetes = new List<Bilhete>();
    private BI sistemaBI = new BI();

    public void CadastrarCliente(Cliente cliente)
    {
        clientes.Add(cliente);
        Console.WriteLine($"Cliente {cliente.Nome} cadastrado com sucesso.");
    }

    public void CadastrarOnibus(Onibus bus)
    {
        onibus.Add(bus);
        Console.WriteLine($"Ônibus {bus.Numero} cadastrado com sucesso.");
    }

    public void EmitirBilhete(Cliente cliente, Bilhete bilhete)
    {
        cliente.AdicionarBilhete(bilhete);
        bilhetes.Add(bilhete);
        sistemaBI.RegistrarBilhete(bilhete);
    }

    public void EmbarcarClienteNoOnibus(string cpfCliente, int numeroOnibus)
    {
        Cliente cliente = BuscarClientePorCPF(cpfCliente);
        Onibus bus = BuscarOnibusPorNumero(numeroOnibus);

        if (cliente != null && bus != null)
        {
            bus.AdicionarPassageiro(cliente);
            cliente.EmbarcarNoOnibus(bus);
        }
        else
        {
            Console.WriteLine("Cliente ou Ônibus não encontrado.");
        }
    }

    public Cliente BuscarClientePorCPF(string cpf)
    {
        return clientes.Find(c => c.CPF == cpf);
    }

    public Onibus BuscarOnibusPorNumero(int numero)
    {
        return onibus.Find(b => b.Numero == numero);
    }

    public void ListarClientes()
    {
        Console.WriteLine("Lista de Clientes:");
        foreach (var cliente in clientes)
        {
            Console.WriteLine(cliente.Nome);
        }
    }

    public void ListarOnibus()
    {
        Console.WriteLine("Lista de Ônibus:");
        foreach (var bus in onibus)
        {
            bus.ExibirOnibus();
        }
    }

    public void ListarBilhetesDeCliente(string cpfCliente)
    {
        Cliente cliente = BuscarClientePorCPF(cpfCliente);
        if (cliente != null)
        {
            cliente.ListarBilhetes();
        }
        else
        {
            Console.WriteLine("Cliente não encontrado.");
        }
    }

    public void GerarRelatorioDeBilhetes()
    {
        sistemaBI.GerarRelatorioDeBilhetes();
    }

    public void GerarRelatorioDePassageiros()
    {
        sistemaBI.GerarRelatorioDePassageiros(onibus);
    }
}

// Business Intelligence
public class BI
{
    private List<Bilhete> bilhetesRegistrados = new List<Bilhete>();

    public void RegistrarBilhete(Bilhete bilhete)
    {
        bilhetesRegistrados.Add(bilhete);
    }

    public void GerarRelatorioDeBilhetes()
    {
        Console.WriteLine("Relatório de Bilhetes Vendidos:");
        var bilhetesPorCidade = bilhetesRegistrados.GroupBy(b => b.CidadeFinal)
            .Select(g => new { Cidade = g.Key, Total = g.Count(), ValorTotal = g.Sum(b => b.Valor) });

        foreach (var item in bilhetesPorCidade)
        {
            Console.WriteLine($"Cidade: {item.Cidade}, Total de Bilhetes: {item.Total}, Valor Total: {item.ValorTotal:C}");
        }
    }

    public void GerarRelatorioDePassageiros(List<Onibus> onibus)
    {
        Console.WriteLine("Relatório de Passageiros por Ônibus:");
        foreach (var bus in onibus)
        {
            Console.WriteLine($"Ônibus {bus.Numero} - Passageiros: {bus.GetPassageiros().Count}");
        }
    }
}

// Programa Principal
public class Program
{
    public static void Main()
    {
        // Criação de endereços
        var endereco1 = new Endereco("Rua A", 123, "Cidade X", "Estado Y", "12345-678");
        var endereco2 = new Endereco("Rua B", 456, "Cidade Y", "Estado Z", "23456-789");

        // Criação de clientes
        var cliente1 = new Cliente("João Silva", "12345678900", "MG1234567", endereco1, FaixaEtaria.Adulto, Sexo.Masculino);
        var cliente2 = new Cliente("Maria Souza", "09876543211", "SP7654321", endereco2, FaixaEtaria.Adulto, Sexo.Feminino);

        // Criação de ônibus
        var onibus1 = new Onibus(1, "ABC-1234", 40, "Cidade X - Cidade Y");
        var onibus2 = new Onibus(2, "DEF-5678", 50, "Cidade Y - Cidade Z");

        // Criação de sistema de bilhetagem
        var sistema = new SistemaBilhetagem();

        // Cadastro de clientes e ônibus no sistema
        sistema.CadastrarCliente(cliente1);
        sistema.CadastrarCliente(cliente2);
        sistema.CadastrarOnibus(onibus1);
        sistema.CadastrarOnibus(onibus2);

        // Criação de bilhetes
        var bilhete1 = new Bilhete(10.50m, DateTime.Now, onibus1, "Cidade X", "Cidade Y");
        var bilhete2 = new Bilhete(15.75m, DateTime.Now, onibus2, "Cidade Y", "Cidade Z");

        // Emissão de bilhetes para clientes
        sistema.EmitirBilhete(cliente1, bilhete1);
        sistema.EmitirBilhete(cliente2, bilhete2);

        // Listar clientes e ônibus
        sistema.ListarClientes();
        sistema.ListarOnibus();

        // Embarque de clientes
        sistema.EmbarcarClienteNoOnibus("12345678900", 1);
        sistema.EmbarcarClienteNoOnibus("09876543211", 2);

        // Listar bilhetes de um cliente
        sistema.ListarBilhetesDeCliente("12345678900");

        // Gerar relatórios de BI
        sistema.GerarRelatorioDeBilhetes();
        sistema.GerarRelatorioDePassageiros();
    }
}
