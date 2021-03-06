---
layout: pt
title: Carnês - API do Boleto Simples
en: /en/references
breadcrumb: Carnês
---

## Carnês

| Recurso                  | Descrição
| ------------------------ | ------------------------
| [POST /api/v1/installments](#criar-carn) | Criar carnê
| [GET /api/v1/installments/:id](#informaes-do-carn) | Informações da carnê
| [GET /api/v1/installments](#listar-carns) | Listar Carnês
| [DELETE /api/v1/installments/:id](#excluir-carn) | Excluir carnê

### Modelo de Dados

| Parâmetro                   | Obr.  | Tipo    | Tamanho | Descrição
| --------------------------- | ----- | ------- | ------- | ------------------------
| **id**                      | N/A   | Integer |         | ID do carnê
| **customer_id**             | Sim   | Integer |         | ID do [Cliente](/reference/v1/customers/)
| **bank_billet_account_id**  | Não   | Integer |         | ID da [Carteira de Cobrança](/reference/v1/bank_billet_accounts/). Se não informado, usará a carteira padrão.
| **amount**                  | Sim   | String  |         | Preço da carnê (R$) Formato: 1.234,34
| **cycle**                   | Não   | String  | 20      | Ciclo da carnê ([possíveis valores](#cycle)). Default: monthly
| **start_at**                | Sim   | Date    |         | Data da Primeira cobrança.
| **end_at**                  | Não   | Date    |         | Data da última cobrança.
| **total**                   | Sim   | Integer |         | Quantidade de parcelas.
| **description**             | Sim   | Text    |         | Descrição do produto vendido ou serviço prestado.
| **instructions**            | Não   | Text    |         | Instruções para o caixa
| **status**                  | Sim   | String  |         | Situação do carnê ([possíveis valores](#status))
| **fine_for_delay**          | Não   | Float   |         | Multa por Atraso
| **late_payment_interest**   | Não   | Float   |         | Juros de Mora
| **bank_billet_layout_id**   | Não   | Integer |         | ID do Modelo de Boleto
| **url**                     | N/A   | String  |         | URL para visualização do carnê
| **bank_billet_ids**         | Não   | Array   |         | IDs de boletos vinculados ao carnê

### Dicionário de Dados

#### cycle

| biweekly   | Quinzenal
| bimonthly  | Bimestral
| monthly    | Mensal
| quarterly  | Trimestral
| semiannual | Semestral
| annual     | Anual

#### status

| created    | Gerando
| processed  | Aberto
| finished   | Finalizado

### Criar carnê

`POST /api/v1/installments`

#### Exemplo de requisição inválida

<ul class="nav nav-tabs" role="tablist">
  <li class="active"><a href="#bash" role="tab" data-toggle="tab">Bash</a></li>
  <!--<li><a href="#ruby" role="tab" data-toggle="tab">Ruby</a></li>
  <li><a href="#php" role="tab" data-toggle="tab">PHP</a></li> -->
</ul>

<div class="tab-content">
  <div class="tab-pane active" id="bash">
    <small>Requisição:</small>

<pre class="bash">
curl -i \
-u $BOLETOSIMPLES_TOKEN:x \
-d '{"installment":{}}' \
-H 'Content-Type: application/json' \
-H 'User-Agent: MyApp (myapp@example.com)' \
-X POST 'https://sandbox.boletosimples.com.br/api/v1/installments'
</pre>

    <small>Resposta:</small>

<pre class="http">
HTTP/1.1 422 Unprocessable Entity
Date: Fri, 17 Oct 2014 18:39:47 GMT
Status: 422 Unprocessable Entity
Content-Type: application/json; charset=utf-8
...

{"errors":{"installment":["não pode ficar em branco"]}}
</pre>
  </div>
  <!--
  <div class="tab-pane" id="ruby">
    <small>Requisição:</small>

<pre class="ruby">
@installment = BoletoSimples::Customer.create({person_name: 'Joao Da Silva'})
if @installment.persisted?
  puts "Sucesso :)"
  puts @installment.attributes
else
  puts "Erro :("
  puts @installment.response_errors
end
</pre>

    <small>Resposta:</small>

<pre class="ruby">
Erro :(
{
  :cnpj_cpf => [
    [0] "não pode ficar em branco"
  ],
  :zipcode => [
    [0] "não pode ficar em branco"
  ]
}
</pre>

  </div>
    <div class="tab-pane" id="php">
      <small>Requisição:</small>

<pre class="php">
$installment = BoletoSimples\Customer::create(['person_name' => 'Joao da Silva']);
if($installment->isPersisted()) {
  echo "Sucesso :)\n";
  print_r($installment->attributes());
} else {
  echo "Erro :(\n";
  print_r($installment->response_errors);
}
</pre>

      <small>Resposta:</small>

<pre class="php">
Erro :(
Array
(
    [cnpj_cpf] => Array
        (
            [0] => não pode ficar em branco
        )

    [zipcode] => Array
        (
            [0] => não pode ficar em branco
        )

)
</pre>

    </div> -->
</div>

#### Exemplo de requisição válida

<ul class="nav nav-tabs" role="tablist">
  <li class="active"><a href="#bash2" role="tab" data-toggle="tab">Bash</a></li>
  <!--<li><a href="#ruby2" role="tab" data-toggle="tab">Ruby</a></li>
  <li><a href="#php2" role="tab" data-toggle="tab">PHP</a></li>-->
</ul>

<div class="tab-content">
  <div class="tab-pane active" id="bash2">
    <small>Requisição:</small>

<pre class="bash">
  curl -i \
  -u $BOLETOSIMPLES_TOKEN:x \
  -d '{"installment":{"customer_id":"1", "bank_billet_account_id": "1", "amount": "1.120,4", "cycle": "monthly", "start_at": "2016-09-15", "total": "3", "description": "Hospedagem"}}' \
  -H 'Content-Type: application/json' \
  -H 'User-Agent: MyApp (myapp@example.com)' \
  -X POST 'https://sandbox.boletosimples.com.br/api/v1/installments'
</pre>

    <small>Resposta:</small>

<pre class="http">
HTTP/1.1 201 Created
Date: Fri, 17 Oct 2014 19:30:06 GMT
Status: 201 Created
Location: https://sandbox.boletosimples.com.br/api/v1/installments/1
Content-Type: application/json; charset=utf-8
...

{
  "id":1,
  "amount":1120.4,
  "cycle":"monthly",
  "start_at":"2016-09-15",
  "end_at":"2016-11-16",
  "instructions":null,
  "customer_id":11,
  "description":"Hospedagem",
  "created_at":"2016-08-15",
  "updated_at":"2016-08-15",
  "created_via_api":true,
  "total":3,
  "bank_billet_account_id":12,
  "status":"created",
  "fine_for_delay": 0.0,
  "late_payment_interest": 0.0
}
</pre>
  </div>
 <!--
  <div class="tab-pane" id="ruby2">
    <small>Requisição:</small>

<pre class="ruby">
@installment = BoletoSimples::Customer.create({
  person_name: "Joao da Silva",
  cnpj_cpf: "782.661.177-64",
  email: "carnê@bom.com",
  address: "Rua quinhentos",
  city_name: "Rio de Janeiro",
  state: "RJ",
  neighborhood: "bairro",
  zipcode: "20071004",
  address_number: "111",
  address_complement: "Sala 4",
  phone_number: "2112123434"
})
if @installment.persisted?
  puts "Sucesso :)"
  puts @installment.attributes
else
  puts "Erro :("
  puts @installment.response_errors
end
</pre>
  <small>Resposta:</small>

<pre class="ruby">
Sucesso :)
{
           "person_name" => "Joao da Silva",
              "cnpj_cpf" => "782.661.177-64",
                 "email" => "carnê@bom.com",
               "address" => "Rua quinhentos",
             "city_name" => "Rio de Janeiro",
                 "state" => "RJ",
          "neighborhood" => "bairro",
               "zipcode" => "20071004",
        "address_number" => "111",
    "address_complement" => "Sala 4",
          "phone_number" => "2112123434",
                    "id" => 67,
         "mobile_number" => nil,
           "person_type" => "individual",
     "mobile_local_code" => nil,
       "created_via_api" => true
}
</pre>
  </div>
  <div class="tab-pane" id="php2">
    <small>Requisição:</small>

<pre class="php">
$installment = BoletoSimples\Customer::create([
  'person_name' => "Joao da Silva",
  'cnpj_cpf' => "860.196.915-19",
  'email' => "carnê@example.com",
  'address' => "Rua quinhentos",
  'city_name' => "Rio de Janeiro",
  'state' => "RJ",
  'neighborhood' => "bairro",
  'zipcode' => "20071004",
  'address_number' => "111",
  'address_complement' => "Sala 4",
  'phone_number' => "2112123434"
]);
if($installment->isPersisted()) {
  echo "Sucesso :)\n";
  print_r($installment->attributes());
} else {
  echo "Erro :(\n";
  print_r($installment->response_errors);
}
</pre>
  <small>Resposta:</small>

<pre class="php">
Sucesso :)
Array
(
    [id] => 66
    [city_name] => Rio de Janeiro
    [person_name] => Joao da Silva
    [address] => Rua quinhentos
    [address_complement] => Sala 4
    [address_number] => 111
    [mobile_number] =>
    [cnpj_cpf] => 860.196.915-19
    [email] => carnê@example.com
    [neighborhood] => bairro
    [person_type] => individual
    [phone_number] => 2112123434
    [zipcode] => 20071004
    [mobile_local_code] =>
    [state] => RJ
    [created_via_api] => 1
)
</pre>
  </div> -->
</div>

### Informações do carnê

`GET /api/v1/installments/:id`

#### Exemplo

<ul class="nav nav-tabs" role="tablist">
  <li class="active"><a href="#bash3" role="tab" data-toggle="tab">Bash</a></li>
  <!--<li><a href="#ruby3" role="tab" data-toggle="tab">Ruby</a></li>
  <li><a href="#php3" role="tab" data-toggle="tab">PHP</a></li>-->
</ul>

<div class="tab-content">
  <div class="tab-pane active" id="bash3">
    <small>Requisição:</small>

<pre class="bash">
curl -i \
-u $BOLETOSIMPLES_TOKEN:x \
-H 'Content-Type: application/json' \
-H 'User-Agent: MyApp (myapp@example.com)' \
-X GET 'https://sandbox.boletosimples.com.br/api/v1/installments/1'
</pre>

    <small>Resposta:</small>

<pre class="http">
HTTP/1.1 200 OK
Date: Fri, 17 Oct 2014 19:46:16 GMT
Status: 200 OK
Content-Type: application/json; charset=utf-8
...

{
  "id":1,
  "amount":1120.4,
  "cycle":"monthly",
  "start_at":"2016-09-15",
  "end_at":"2016-11-16",
  "instructions":null,
  "customer_id":11,
  "description":"Hospedagem",
  "created_at":"2016-08-15",
  "updated_at":"2016-08-15",
  "created_via_api":true,
  "total":3,
  "bank_billet_account_id":12,
  "status":"created",
  "fine_for_delay": 0.0,
  "late_payment_interest": 0.0
}
</pre>
  </div>
  <!--
  <div class="tab-pane" id="ruby3">
    <small>Requisição:</small>

<pre class="ruby">
@installment = BoletoSimples::Customer.find(67)
puts @installment.attributes
</pre>

    <small>Resposta:</small>

<pre class="ruby">
{
             "city_name" => "Rio de Janeiro",
           "person_name" => "Joao da Silva",
               "address" => "Rua quinhentos",
    "address_complement" => "Sala 4",
        "address_number" => "111",
         "mobile_number" => nil,
              "cnpj_cpf" => "782.661.177-64",
                 "email" => "carnê@bom.com",
          "neighborhood" => "bairro",
           "person_type" => "individual",
          "phone_number" => "2112123434",
               "zipcode" => "20071004",
     "mobile_local_code" => nil,
                 "state" => "RJ",
       "created_via_api" => true,
                    "id" => 67
}
</pre>
  </div>
  <div class="tab-pane" id="php3">
    <small>Requisição:</small>

<pre class="php">
$installment = BoletoSimples\Customer::find(66);
print_r($installment->attributes());
</pre>

    <small>Resposta:</small>

<pre class="php">
Array
(
    [id] => 66
    [city_name] => Rio de Janeiro
    [person_name] => Joao da Silva
    [address] => Rua quinhentos
    [address_complement] => Sala 4
    [address_number] => 111
    [mobile_number] =>
    [cnpj_cpf] => 860.196.915-19
    [email] => carnê@example.com
    [neighborhood] => bairro
    [person_type] => individual
    [phone_number] => 2112123434
    [zipcode] => 20071004
    [mobile_local_code] =>
    [state] => RJ
    [created_via_api] => 1
)
</pre>
  </div>
  -->
</div>

### Listar Carnês

`GET /api/v1/installments`

<table class='table table-bordered'>
  <thead>
    <tr>
      <th>Parâmetro</th>
      <th data-container="body" data-toggle="tooltip" title="Obrigatório">Obr.</th>
      <th>Tipo</th>
      <th>Descrição</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>
        <strong>page </strong>
      </td>
      <td>
        Não
      </td>
      <td>
        Number
      </td>
      <td>
        Número da Página
      </td>
    </tr>

    <tr>
      <td>
        <strong>per_page </strong>
      </td>
      <td>
        Não
      </td>
      <td>
        Number
      </td>
      <td>
        Quantidade de registros por página (Maximo de 250)
      </td>
    </tr>
  </tbody>
</table>

#### Exemplo

<ul class="nav nav-tabs" role="tablist">
  <li class="active"><a href="#bash6" role="tab" data-toggle="tab">Bash</a></li>
  <!--<li><a href="#ruby6" role="tab" data-toggle="tab">Ruby</a></li>
  <li><a href="#php6" role="tab" data-toggle="tab">PHP</a></li>-->
</ul>

<div class="tab-content">
  <div class="tab-pane active" id="bash6">
    <small>Requisição:</small>

<pre class="bash">
curl -i \
-u $BOLETOSIMPLES_TOKEN:x \
-H 'Content-Type: application/json' \
-H 'User-Agent: MyApp (myapp@example.com)' \
-X GET "https://sandbox.boletosimples.com.br/api/v1/installments?page=1&per_page=50"
</pre>

    <small>Resposta:</small>

<pre class="http">
HTTP/1.1 200 OK
Date: Fri, 17 Oct 2014 19:46:16 GMT
Status: 200 OK
Link: <https://sandbox.boletosimples.com.br/api/v1/installments?page=3&per_page=50>; rel="last", <https://sandbox.boletosimples.com.br/api/v1/installments?page=2&per_page=50>; rel="next"
Total: 101
Content-Type: application/json; charset=utf-8
...

[
  {
    "id":1,
    "amount":1120.4,
    "cycle":"monthly",
    "start_at":"2016-09-15",
    "end_at":"2016-11-16",
    "instructions":null,
    "customer_id":11,
    "description":"Hospedagem",
    "created_at":"2016-08-15",
    "updated_at":"2016-08-15",
    "created_via_api":true,
    "total":3,
    "bank_billet_account_id":12,
    "status":"created",
    "fine_for_delay": 0.0,
    "late_payment_interest": 0.0
  }
]
</pre>
  </div>
 <!--
  <div class="tab-pane" id="ruby6">
    <small>Requisição:</small>

<pre class="ruby">
@installments = BoletoSimples::Customer.all(page: 1, per_page: 2)
puts "Carnês Retornados: #{@installments.count}"
puts "Total: #{BoletoSimples.last_request.total}"
puts "Primeira Página: #{BoletoSimples.last_request.links[:first]}"
puts "Página Anterior: #{BoletoSimples.last_request.links[:prev]}"
puts "Próxima Página: #{BoletoSimples.last_request.links[:next]}"
puts "Última Página: #{BoletoSimples.last_request.links[:last]}"
</pre>

    <small>Resposta:</small>

<pre class="http">
Carnês Retornados: 2
Total: 9
Primeira Página:
Página Anterior:
Próxima Página: https://sandbox.boletosimples.com.br/api/v1/installments?page=2&per_page=2
Última Página: https://sandbox.boletosimples.com.br/api/v1/installments?page=5&per_page=2
</pre>
  </div>
  <div class="tab-pane" id="php6">
    <small>Requisição:</small>

<pre class="php">
$installments = BoletoSimples\Customer::all(['page' => 1, 'per_page' => 2]);
echo "Carnês Retornados: " . sizeof($installments) . "\n";
echo "Total: " . BoletoSimples::$last_request->total . "\n";
echo "Primeira Página: " . BoletoSimples::$last_request->links['first'] . "\n";
echo "Página Anterior: " . BoletoSimples::$last_request->links['prev'] . "\n";
echo "Próxima Página: " . BoletoSimples::$last_request->links['next'] . "\n";
echo "Última Página: " . BoletoSimples::$last_request->links['last'] . "\n";
</pre>

    <small>Resposta:</small>

<pre class="http">
Carnês Retornados: 2
Total: 9
Primeira Página:
Página Anterior:
Próxima Página: https://sandbox.boletosimples.com.br/api/v1/installments?page=2&per_page=2
Última Página: https://sandbox.boletosimples.com.br/api/v1/installments?page=5&per_page=2
</pre>
  </div>
  -->
</div>

### Excluir carnê

`DELETE /api/v1/installments/:id`

Nenhum boleto gerado pela carnê será excluído.

#### Exemplo

<ul class="nav nav-tabs" role="tablist">
  <li class="active"><a href="#bash2" role="tab" data-toggle="tab">Bash</a></li>
  <!--li><a href="#ruby2" role="tab" data-toggle="tab">Ruby</a></li>
  <li><a href="#php2" role="tab" data-toggle="tab">PHP</a></li-->
</ul>

<div class="tab-content">
  <div class="tab-pane active" id="bash2">
    <small>Requisição:</small>

<pre class="bash">
curl -i \
-u $BOLETOSIMPLES_TOKEN:x \
-H 'User-Agent: MyApp (myapp@example.com)' \
-X DELETE 'https://sandbox.boletosimples.com.br/api/v1/installments/1'
</pre>

    <small>Resposta:</small>

<pre class="http">
HTTP/1.1 204 No Content
Date: Fri, 17 Oct 2014 19:30:06 GMT
Status: 204 No Content
...

</pre>
  </div>
  <!--div class="tab-pane" id="ruby2">
    <small>Requisição:</small>

<pre class="ruby">
@customer = BoletoSimples::Customer.create({
  person_name: "Joao da Silva",
  cnpj_cpf: "782.661.177-64",
  email: "cliente@bom.com",
  address: "Rua quinhentos",
  city_name: "Rio de Janeiro",
  state: "RJ",
  neighborhood: "bairro",
  zipcode: "12312-123",
  address_number: "111",
  address_complement: "Sala 4",
  phone_number: "2112123434"
})
if @customer.persisted?
  puts "Sucesso :)"
  puts @customer.attributes
else
  puts "Erro :("
  puts @customer.response_errors
end
</pre>
  <small>Resposta:</small>

<pre class="ruby">
Sucesso :)
{
           "person_name" => "Joao da Silva",
              "cnpj_cpf" => "782.661.177-64",
                 "email" => "cliente@bom.com",
               "address" => "Rua quinhentos",
             "city_name" => "Rio de Janeiro",
                 "state" => "RJ",
          "neighborhood" => "bairro",
               "zipcode" => "12312-123",
        "address_number" => "111",
    "address_complement" => "Sala 4",
          "phone_number" => "2112123434",
                    "id" => 67,
         "mobile_number" => nil,
           "person_type" => "individual",
     "mobile_local_code" => nil,
       "created_via_api" => true
}
</pre>
  </div>
  <div class="tab-pane" id="php2">
    <small>Requisição:</small>

<pre class="php">
$customer = BoletoSimples\Customer::create([
  'person_name' => "Joao da Silva",
  'cnpj_cpf' => "860.196.915-19",
  'email' => "cliente@example.com",
  'address' => "Rua quinhentos",
  'city_name' => "Rio de Janeiro",
  'state' => "RJ",
  'neighborhood' => "bairro",
  'zipcode' => "12312-123",
  'address_number' => "111",
  'address_complement' => "Sala 4",
  'phone_number' => "2112123434"
]);
if($customer->isPersisted()) {
  echo "Sucesso :)\n";
  print_r($customer->attributes());
} else {
  echo "Erro :(\n";
  print_r($customer->response_errors);
}
</pre>
  <small>Resposta:</small>

<pre class="php">
Sucesso :)
Array
(
    [id] => 66
    [city_name] => Rio de Janeiro
    [person_name] => Joao da Silva
    [address] => Rua quinhentos
    [address_complement] => Sala 4
    [address_number] => 111
    [mobile_number] =>
    [cnpj_cpf] => 860.196.915-19
    [email] => cliente@example.com
    [neighborhood] => bairro
    [person_type] => individual
    [phone_number] => 2112123434
    [zipcode] => 12312-123
    [mobile_local_code] =>
    [state] => RJ
    [created_via_api] => 1
)
</pre>
  </div-->
</div>