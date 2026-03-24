# Projeto-Simulador-de-Concorr-ncia-e-Exclus-o-M-tua-em-Java
Objetivo Este projeto foi desenvolvido para demonstrar na prática os conceitos de Sistemas Operacionais aplicados ao desenvolvimento de software, especificamente o gerenciamento de Regiões Críticas e a prevenção de Condições de Corrida (Race Conditions). 
%%bash

mkdir -p java_src

cat > java_src/SistemaTriagem.java << EOF
class SistemaTriagem {
    // Recurso Compartilhado: O nível de prioridade do paciente
    private String prioridadeAtual = "Não Definida";

    // Região Crítica: Apenas um médico (Thread) pode atualizar por vez
    public synchronized void atualizarPrioridade(String nomeMedico, String novaPrioridade) {
        System.out.println("\n[LOG] Médico(a) " + nomeMedico + " acessando prontuário...");

        // Simula o tempo de análise da foto/exame (como no seu protótipo de skin lesions)
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        this.prioridadeAtual = novaPrioridade;
        System.out.println("[SISTEMA] Prioridade atualizada para: " + prioridadeAtual + " por " + nomeMedico);
    }
}
EOF

cat > java_src/Medico.java << EOF
class Medico extends Thread {
    private String nome;
    private String analise;
    private SistemaTriagem sistema;

    public Medico(String nome, String analise, SistemaTriagem sistema) {
        this.nome = nome;
        this.analise = analise;
        this.sistema = sistema;
    }

    @Override
    public void run() {
        sistema.atualizarPrioridade(nome, analise);
    }
}
EOF

cat > java_src/Main.java << EOF
public class Main {
    public static void main(String[] args) {
        SistemaTriagem prontoSocorro = new SistemaTriagem();

        // Simulação: Médico 1 analisa uma lesão leve, Médico 2 analisa uma emergência
        Medico m1 = new Medico("Maria", "VERDE (Pouco Urgente)", prontoSocorro);
        Medico m2 = new Medico("Dr. Silva", "VERMELHO (Emergência)", prontoSocorro);

        System.out.println("--- Início do Processamento de Triagem ---");
        m1.start();
        m2.start();
    }
}
EOF

javac java_src/*.java
java -cp java_src Main
