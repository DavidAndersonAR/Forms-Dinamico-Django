# Forms-Dinamico-Django


Arquivo forms.py:

```sh
class TipoLocalidadeModelForm(forms.ModelForm):
    class Meta:
        model = TipoLocal
        fields = '__all__'
```

Arquivo view.py com a função e classe que precisamos:

```sh
# Tipo de Localidade
def tipo_localidade_view(request):
    tipo_localidade = TipoLocal.objects.all()
    return render(request, 'tipo_localidade.html', {'tipo_localidade':tipo_localidade})

class TipoLocalidadeUpdateView(UpdateView):
    model = TipoLocal
    form_class = TipoLocalidadeModelForm
    template_name = 'tipo_local_update.html'
    success_url = '/tipo_localidade+list/'
```

Form no arquivo HTML:

```sh
<form method="post" enctype="multipart/form-data">
      {% csrf_token %}
      <h1>Editar Formulario</h1>
      <table>
        {% for field in form %}
        <tr>
          <td><label for="id_{{ field.name }}">{{ field.label }}</label></td>
          <td>{{ field }}</td>
        </tr>
        {% endfor %}
      </table>
      <div>
        <div class="buttons-container">
          <input type="submit" value="Salvar Alterações" class="btn btn-primary">
          <a href="#" class="btn btn-danger">Deletar</a>
          <a href="#" class="btn btn-secondary btn-cancelar">Cancelar</a>
        </div>
      </div>
    </form>
```
Codigo Javascript:

```sh
$(document).ready(function(){
                // Responsável por popular o campo de empresa com base no país selecionado
                $('#id_pais').change(function(){
                    var id_pais = $(this).val();
                    $.ajax({
                        url: "{% url 'selcionar_id' %}",
                        data: {
                            'id_pais': id_pais
                        },
                        dataType: 'json',
                        success: function(data){
                            var empresa_select = $('#id_empresa');
                            empresa_select.empty();
                            empresa_select.append($('<option>').text('Selecione um item...').attr('value', ''));
                            $.each(data.empresa, function(index, item){
                                empresa_select.append($('<option>').text(item.empresa).attr('value', item.id));
                            });
                        }
                    });
                });   
            });
```
Função no arquivo view.py que recebe a requisição js:

```sh
def selcionar_id(request):
    for requisicao in request.GET:
        name = requisicao
        if name == 'id_pais':
            id_pais = request.GET.get('id_pais')
            if request.GET.get('id_contrato'):
                contrato = request.GET.get('id_contrato')
                empresa = Empresa.objects.filter(pais_id=id_pais, ativo_id=1, contrato_id = contrato)
            else:
                empresa = Empresa.objects.filter(pais_id=id_pais, ativo_id=1)
            data = {'empresa':list(empresa.values('id','empresa'))}
            print(data)
            
	    return JsonResponse(data)
```

