---
routeAlias: budget
---

<Kicker>02 · процесс</Kicker>

# Медленный контур: бюджет ошибок

<div class="grid grid-cols-2 gap-10 mt-6">

<div class="flex items-center gap-5">
<div class="text-6xl font-bold font-mono text-link leading-none whitespace-nowrap">43</div>
<div class="text-muted leading-snug">минуты простоя за 30 дней<br>допускает SLO 99,9 %</div>
</div>

<div class="flex items-center gap-5">
<div class="text-6xl font-bold font-mono text-link leading-none whitespace-nowrap">70 %</div>
<div class="text-muted leading-snug">сбоев вызваны изменениями —<br>оценка Google<Cite n="5" /></div>
</div>

</div>

<div class="font-mono text-xs font-bold tracking-wider text-muted mt-10 mb-3">пример политики: расход бюджета → воздействие регулятора</div>

<div class="grid grid-cols-4 gap-4">
<Card><div class="text-3xl font-bold font-mono text-link">25 %</div>Оповестить владельца</Card>
<Card><div class="text-3xl font-bold font-mono text-link">50 %</div>Приостановить эксперименты</Card>
<Card><div class="text-3xl font-bold font-mono text-link">75 %</div>Заморозить новые функции</Card>
<Card><div class="text-3xl font-bold font-mono text-link">100 %</div>Остановить релизы</Card>
</div>

<p class="text-xl font-bold text-link mt-8">Чем сильнее отклонение, тем жёстче воздействие.</p>

<!--
Второй контур работает не с экземплярами, а с людьми и процессом разработки. Бюджет ошибок — дополнение SLO до ста процентов. Регулируемая величина здесь — темп изменений, потому что именно изменения чаще всего запускают отказы. Чем сильнее отклонение, тем жёстче воздействие: это та же отрицательная обратная связь, только исполнительное звено — решения людей, а постоянная времени измеряется неделями. Окно скользящее, здесь 30 дней: 0,1 % от них и есть 43 минуты. Часто берут четыре недели, тогда бюджет 40 минут.

Ступени на слайде — мой пример политики, а не цитата: в книге SRE такой лестницы нет.

Справка, бывший текст слайда. Подписи и ступени сокращены, полные формулировки проговаривать голосом.

43 — минуты полной недоступности в месяц, которые допускает SLO 99,9 %. 70 % сбоев вызваны изменениями в работающей системе — оценка Google [5].

Ступени: 25 % — оповестить владельца, разобрать причины. 50 % — приостановить рискованные эксперименты. 75 % — заморозить новые функции, оставить надёжность. 100 % — остановить релизы до восстановления бюджета.

Чем сильнее отклонение, тем жёстче воздействие. Только исполняют здесь не машины, а люди, и время реакции измеряется неделями.
-->
