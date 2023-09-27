---
title: "Minha experiência com a DCPT"
classes: wide
header:
  teaser: /assets/images/DCPT/00.png
  overlay_image: /assets/images/DCPT/00.png
  overlay_filter: 0.5
ribbon: DarkSlateGray
excerpt: "Nessa postagem falo um pouco sobre a minha experiência com a DCPT, desde a preparação até a realização do exame"
description: "Nessa postagem falo um pouco sobre a minha experiência com a DCPT, desde a preparação até a realização do exame"
categories:
  - Review
tags:
  - Certificação
  - DESEC
  - DCPT
toc: true
toc_sticky: true
toc_label: "Sumário"
toc_icon: "terminal"
---

Nessa postagem, vou falar um pouco sobre toda a minha experiência com a DCPT, desde a preparação até a realização da prova, e passar algumas dicas que acho interessante pra quem pensa em tirar essa certificação

# O curso

Quando eu decidi fazer a DCPT eu ainda não trabalhava na área de segurança, e busquei uma certificação justamente para me ajudar a fazer essa transação, tanto na parte de aprendizado quanto no currículo, optei por começar com a DCPT justamente por não ter a barreira do idioma e por indicação de conhecidos

Na época eu comprei um pacote onde vinha todos os cursos da DESEC juntamente com a certificação, paguei R$ 3.500,00 (se não me engano) e logo iniciei o curso preparatório para a prova

O curso é bem grande e aborda todo o processo da realização de um pentest, e ele trás uma visão mais voltada para um profissional, não só apenas para a exploração das vulnerabilidades. Eu particularmente gostei bastante, como estava no início da minha jornada nessa área, aprendi muito durante essa fase

# Pentest Experience

Após o termino do curso, você vai para o Pentest Experience, que são 9 projetos que você precisa concluir para só então liberar a prova, esse projetos são feitos simulando uma contratação sua por uma empresa, então é passado um escopo e um nda e você faz o pentest, nessa fase não tem mais aulas e aqui você não aprende mais nada, só coloca em prática tudo que viu durante o curso

Confesso que demorei bastante para terminar esses projetos, nesse meio tempo eu comecei a trabalhar como pentester e várias demandas foram surgindo, e os projetos sempre ficavam para depois

Uma dica para quem está nessa parte é: documente todo o processo de exploração, é uma boa oportunidade de treinar a escrita de relatórios. Também, tente resolver esses projetos sem usar o metasploit, é bom pra já ir treinando para a prova

Peguei alguns projetos na vida real bem parecidos com os vistos aqui, então, para mim, o Pentest Experience foi bem proveitoso também, porque acabou me ajudando um pouco no trabalho

Após alguns logos meses, finalmente terminei os 9 projetos, chegou a hora de marcar a prova

# A prova

No total, são 48 horas de exame: 24 horas para execução da prova + 24 horas para a confecção e envio do relatório; e durante a prova não é permitido a utilização de nenhuma ferramenta automatizada (metasploit, sqlmap, atc...), ou seja, toda a exploração deve ser feita de forma manual. Para ser aprovado no exame é necessário resolver 100% dos desafios propostos, que no total são 5 máquinas

Após terminar o Pentest Experience eu estava bem ansioso para fazer a prova, até porque eu já havia comprado o voucher a um bom tempo e queria logo fazer a prova para pegar a certificação, então agendei o exame para o primeiro final de semana disponível, o que seria uma semana depois

A semana que eu marquei a prova acabou sendo uma semana bem conturbada, precisei fazer uma viagem a trabalho que durou 2 dias e voltando de viagem tive que resolver outras demandas, o que fez com que eu chegasse no final da semana bem cansado, pensei em remarcar a prova pra outra data pra poder descansar melhor mas a ansiedade falou mais alto e resolvi manter a data, então no sábado comecei a prova

Iniciando o exame peguei a primeira máquina e já comecei a fazer o recon, enumerei as portas abertas, enumerei diretórios... e? nada... mesmo com as informações na minha frente não consegui pensar em nenhuma linha de ação, então pensei que talvez não estivesse vendo algo e resolvi ir para a próxima máquina, então fiz a enumeração e também não consegui pensar em nada, isso se repetiu com a 3ª, 4ª e 5ª máquina, após algumas boas horas nesse processo cheguei a conclusão que na verdade eu precisava descansar, porque muito provavelmente isso estava acontecendo por causa do cansaço, então, dei uma pausa no exame e fui tirar uma "cochileta russa", acontece que quando eu acordei o exame estava quase no final, e mesmo que eu resolvesse as máquinas bem rápido não daria tempo de fazer as 5, nesse momento já aceitei que não seria dessa vez eu passaria na prova

Então aqui vai uma dica: respeite seu corpo e se prepare para a prova, descanse bem e caso não se sinta 100% remarque a prova. As 24 horas de exame passam muito rápido, então, qualquer problema que acontecer pode resultar na sua reprovação

# A prova - retake

Após a minha reprovação, pensei até e não realizar o exame novamente, fiquei um pouco frustrado e também desanimado. Passados 4 dias eu parei pra refletir sobre o acontecido e então decidi refazer a prova, peguei o retake (se não me engano foi R$ 350,00) e agendei novamente a prova

Dessa vez me preparei adequadamente, descansei e dormi bem no dia anterior, então, no sábado novamente comecei a prova, dessa vez todas a máquinas foram diferentes da primeira

Iniciei a prova realizando a enumeração e levantando algumas informações e consegui seguir um caminho lógico pra explorar a primeira máquina, acabei resolvendo esse desafio relativamente rápido, o que foi me dando confiança, então parti para a segunda máquina e essa foi a máquina de buffer overflow, ele é feito em um ambiente Linux e não é nada muito complexo, então também consegui resolver de forma rápida

Fiz uma pausa, e depois voltei para fazer as outras 3 máquinas, a máquina 3 e a máquina 4 também foram seguindo um caminho lógico para exploração e a última foi a que deu mais trabalho, pois tinham alguns passos a mais e acabou demorando um pouco, mas também consegui resolver sem problemas

Agora sim, consegui, bom, fiquei aliviado e me senti um pouco mais confiante, terminei a prova relativamente rápido, ainda faltavam boas horas, então fui dormir e no outro dia fiz o relatório

# Dicas

- Faça anotações durante o curso

	Tudo o que você precisa para fazer a prova é ensinado durante o curso, então, é interessante que você anote os comandos e algumas informações que achar interessante para que possa consultar durante a prova, principalmente aqueles que são vistos no inicio do curso ;)

- Faça uma boa gestão de tempo

	24 horas de prova são mais do que suficientes para a realização do exame, mas é necessário que você consiga gerenciar bem o seu tempo, porque qualquer problema pode acabar ocasionando a sua reprovação, uma dica valiosa é: durante a prova, antes de enviar a última flag, se certifique que você já tem tudo o que é necessário para fazer o relatório. Quando você envia a última flag, a plataforma "fecha" e inicia a contagem das 24 horas para entregar o relatório, independente de quanto tempo você ainda tinha, então preste atenção antes de enviar a última flag

- Não se preocupe com o buffer overflow

	Vejo muita gente preocupada com a máquina de buffer overflow, mas como falei na primeira dica, tudo o que cai na prova é visto durante o curso, então o buffer overflow também é parecido com o abordado lá, caso você ainda não tenha confiança, procure sobre "buffer overflow vanilla" vai encontrar conteúdos que te ajudarão

- Se possível, faça CTFs

	Muitas máquinas da prova seguem o mesmo padrão de alguns CTFs que a gente vê por ai (recon, encontrar um serviço, explorar, etc..) então quanto mais prática com isso você tiver, melhor. Obs: nem todas as máquinas da prova precisam escalar privilégios