# Projeto-Simulador-de-Concorr-ncia-e-Exclus-o-M-tua-em-Java
Objetivo Este projeto foi desenvolvido para demonstrar na prática os conceitos de Sistemas Operacionais aplicados ao desenvolvimento de software, especificamente o gerenciamento de Regiões Críticas e a prevenção de Condições de Corrida (Race Conditions). 
%%bash

mkdir -p java_src

cat > java_src/Impressora.java << EOF
class Impressora {
    // A Região Crítica é este método.
    // O 'synchronized' garante que apenas UM Aluno use a impressora por vez.
    public synchronized void imprimirDocumento(String nomeAluno, int paginas) {
        System.out.println("\n[SISTEMA] " + nomeAluno + " começou a imprimir...");

        for (int i = 1; i <= paginas; i++) {
            System.out.println(nomeAluno + " imprimindo página " + i + "...");
            try {
                Thread.sleep(500); // Simula o tempo da impressora funcionando
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        System.out.println("[SISTEMA] " + nomeAluno + " FINALIZOU a impressão.");
    }
}
EOF

cat > java_src/Aluno.java << EOF
class Aluno extends Thread {
    private String nome;
    private Impressora impressora;

    public Aluno(String nome, Impressora impressora) {
        this.nome = nome;
        this.impressora = impressora;
    }

    @Override
    public void run() {
        // Cada aluno tenta imprimir 3 páginas
        impressora.imprimirDocumento(nome, 3);
    }
}
EOF

cat > java_src/Main.java << EOF
public class Main {
    public static void main(String[] args) {
        Impressora multifuncional = new Impressora();

        // Criando dois processos (Alunos) que compartilham a mesma impressora
        Aluno aluno1 = new Aluno("Maria", multifuncional);
        Aluno aluno2 = new Aluno("João", multifuncional);

        System.out.println("--- Iniciando Fila de Impressão ---");
        aluno1.start();
        aluno2.start();
    }
}
EOF

javac java_src/*.java
java -cp java_src Main
