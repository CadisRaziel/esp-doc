# Criando projeto
- Ao clicar na aba `wizard` vai ter o botao `new project`
- Clique nele e o visual studio code vai pedir para selecionar o framework configurado (que no caso eu configuei pelo tutorial do site)
https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/linux-macos-setup.html
- Ao fazer isso ele vai pedir uma pasta pra salvar o projeto.. e pronto ele ja vai fazer todo o restante


# Selecionando target
- Na barra debaixo do visual studio code vai ter o icone de um `processador`(set espressif device target (IDF_TARGET)) clique nele e selecione `esp32` 
- Custom board (board no board selected / target=esp32)
- Isso basicamente diz ao sistema: “Estou usando um ESP32 padrão via USB” Sem hardware especial de debug.

# Selecionando porta USB
- Quando a esp ta conectada no `UBS` e necessario selecionar a porta
- No linux a porta mais comum sao:
| Porta                | Significado                                                                           |
| -------------------- | ------------------------------------------------------------------------------------- |
| `/dev/ttyUSB0/1/...` | Conversor USB-Serial baseado em **CP2102 / CH340** (mais comum)                       |
| `/dev/ttyACM0`       | Geralmente boards como Arduino Leonardo / Micro, STM32, RP2040 na maioria das ESP32   |

- No seu print
/dev/ttyUSB1 -> ícone de plug 
/dev/ttyACM0 -> 1a86 (fabricante do CH340)  mas não é porta de dados programável

- A que você deve escolher é:
/dev/ttyUSB1

- Essa é a porta certa pra:
Gravar o firmware
Monitorar a saída serial
Reset automático


# Entendendo estrutura do projeto
--------- DEVCONTAINER ---------
#### .devcontainer -> Essa pasta é usada para devcontainer do VS Code, ou seja, para criar um ambiente de desenvolvimento isolado via Docker. Isso é útil para ESP-IDF porque garante que você tenha todas as ferramentas corretas sem poluir seu sistema. (Em resumo, .devcontainer permite rodar seu projeto em um ambiente Linux consistente mesmo que você esteja no Windows/macOS.)

- devcontainer.json -> Configurações do contêiner
dockerfile: qual Dockerfile usar.
customizations.vscode.extensions: extensões que devem ser instaladas dentro do contêiner.
runArgs: argumentos do Docker (aqui, --privileged para acesso a hardware USB).
idf.*: caminhos internos dentro do contêiner para o ESP-IDF e ferramentas.

- dockerfile:
Define a imagem base (espressif/idf) e instala dependências.
Configura o ambiente (~/.bashrc) para carregar o ESP-IDF automaticamente.
ENTRYPOINT aponta para script de inicialização do ESP-IDF dentro do contêiner.
--------- DEVCONTAINER ---------

--------- VSCODE ---------
#### .vscode -> Configurações específicas do Visual Studio Code para este projeto.

- c_cpp_properties.json:
Configura o IntelliSense para o ESP-IDF:
compilerPath: o compilador C/C++ do ESP32.
includePath: onde procurar headers (.h) para completar código.
browse.path: caminhos para indexação de símbolos.

- launch.json:
Configurações de depuração:
gdbtarget -> adapter genérico para GDB.
espidf -> para rodar/debuggar a aplicação ESP-IDF diretamente.

- settings.json:
Configurações do VS Code relacionadas ao projeto:
Caminhos do ESP-IDF (idf.espIdfPath), ferramentas (idf.toolsPath), Python.
Configuração extra (IDF_TARGET=esp32) que define qual chip você está usando.
Configurações do clangd para C/C++ (auto-complete e indexação).
--------- VSCODE ---------


--------- MAIN ---------
#### Aqui esta o codigo da minha aplicacao

- CMakeLists.txt:
Declara que este é um componente IDF (idf_component_register) e quais arquivos .c estão incluídos (hello_world_main.c).
Define dependências (PRIV_REQUIRES spi_flash) e diretórios de include.

- hello_world_main.c:
Código principal do seu ESP32.
A função app_main() é equivalente ao main() em C normal.
Exibe informações do chip, memória e reinicia após 10 segundos.
Usa FreeRTOS (vTaskDelay) para gerenciar delay.
--------- MAIN ---------


--------- BUILD ---------
- Bootloader
bootloader/ -> vazio normalmente, mas pode receber arquivos temporários de build do bootloader.
bootloader-prefix/ -> arquivos temporários e de stamp do bootloader:
src/bootloader-stamp/ -> arquivos de controle do build do bootloader:
bootloader-patch-info.txt -> se o bootloader foi patchado ou modificado.
bootloader-source_dirinfo.txt -> infos sobre a origem dos arquivos do bootloader.
bootloader-update-info.txt -> controle de atualização incremental.
tmp/ -> scripts temporários gerados pelo CMake para criar o bootloader:
bootloader-cfgcmd.txt -> comandos de configuração do bootloader.
bootloader-mkdirs.cmake -> instruções para criar diretórios temporários.
Resumo: Toda essa estrutura é usada para compilar e atualizar o bootloader sem recompilar tudo sempre.


- CMakeFiles/
Essa pasta é gerada pelo CMake e contém arquivos de build internos.
Pastas:
bootloader.dir/ -> objetos do bootloader.
hello_world.elf.dir/ -> objetos da sua aplicação principal.
git-data/, pkgRedirects/, esp-idf/ -> caches internos do CMake e ESP-IDF.
Arquivos importantes:
build.ninja, rules.ninja, .ninja_lock -> regras usadas pelo Ninja, que é o build system padrão do ESP-IDF.
CMakeCache.txt -> cache do CMake, contém caminhos, flags e configurações de compilação.
compile_commands.json -> usado pelo VS Code e clangd para auto-complete, mostra como cada arquivo C é compilado.
cmake_install.cmake -> instruções de instalação (geralmente usadas para copiar arquivos durante o build).
app-flash_args, bootloader-flash_args -> argumentos que o script de flash usa para gravar a ESP32.
Resumo: Aqui o CMake organiza todos os objetos compilados, dependências, scripts e logs internos do build.


- Arquivos de configuração de flash
flash_args, flash_args.in, flash_app_args, flash_bootloader_args, flash_project_args, partition-table-flash_args, flasher_args.json
-> todos esses arquivos definem como o ESP-IDF vai gravar o binário na sua ESP32:
Endereço da memória,
Ordem de gravação (bootloader, app, partições),
Flags da ferramenta esptool.py.
Sempre que você dá idf.py flash, esses arquivos são usados por baixo do capô.

- Arquivos kconfigs*
kconfigs.in e kconfigs_projbuild.in
-> configuram opções do SDK (sdkconfig) para o projeto.
O ESP-IDF usa o Kconfig, um sistema de configuração parecido com o Linux kernel.
Gera o sdkconfig final que você vê na raiz.

- ldgen_libraries e ldgen_libraries.in
Relacionado a linker scripts e bibliotecas.
O ESP-IDF gera instruções de ligação do ELF, garantindo que os segmentos de memória (text, data, bss) fiquem no lugar certo da flash/ram.

- project_description.json
Contém informações do projeto:
Nome, versão do ESP-IDF,
Caminhos dos componentes,
Dependências.
Usado internamente pelo CMake e VS Code para integração com o ESP-IDF.

- Outros arquivos importantes
config.env -> variáveis de ambiente usadas durante o build.
log/ -> logs do CMake/Ninja, útil para debug de build.
gdbinit -> configuração de depuração GDB.
TargetDirectories.txt -> paths de saída do build.

- Resumo geral
bootloader* -> tudo que é necessário para o bootloader do ESP32.
CMakeFiles/ -> objetos compilados, scripts Ninja, cache do CMake, ELF, e arquivos temporários.
flash_* -> scripts/args para gravar a ESP32.
kconfigs* e ldgen_libraries* -> configuração do SDK e linker scripts.
project_description.json e config.env -> metadata do projeto.
--------- BUILD ---------

--------- ARQUIVOS SOLTOS NA RAIZ ---------
#### Todos esses arquivos sao criados na raiz

- clangd:
Configuração do clangd, que o VS Code usa para auto-complete e navegação de código C/C++.
Aqui você remove flags específicas (-f*, -m*) para evitar conflitos com ESP32.

- CMakeLists.txt (raiz do projeto):
Arquivo principal do CMake para o ESP-IDF.
Chama o project.cmake do ESP-IDF, que gerencia compilação, linking, flashing, etc.
idf_build_set_property(MINIMAL_BUILD ON) -> mantém build mínima com só o necessário.

- pytest_hello_world.py:
Testes automáticos para o projeto, incluindo testes em QEMU (emulador) e host.
Verifica se a saída do seu ESP32 é a esperada (Hello world!) e se o ELF gerado bate com SHA256.
Normalmente usado para Continuous Integration (CI) e não afeta compilação local.

- sdkconfig, sdkconfig.ci, sdkconfig.old:
Arquivos de configuração do ESP-IDF:
sdkconfig: configura opções do ESP32 (Wi-Fi, Bluetooth, GPIO defaults etc.).
sdkconfig.ci: usado em pipelines de CI.
sdkconfig.old: backup da configuração anterior.
--------- ARQUIVOS SOLTOS NA RAIZ ---------