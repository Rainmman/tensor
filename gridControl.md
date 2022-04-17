# Таблицы

С помощью контрола "Таблица" можно отобразить данные из разлиных источников в виде таблицы.

Рассмотрим основные опции, которые вы можете настроить у таблицы.

## Источник данных

Самое главное, что нужно для таблицы - данные. Чтобы их подгрузить, нужен объект [source](https://wasaby.dev/docs/js/Controls/grid/View/options/source/), реализующий интерфейс Types/source:ICrud.

Одновременно с source нужно указать имя поля с первичным ключом - [keyProperty](https://wasaby.dev/docs/js/Controls/grid/View/options/keyProperty/). Обычно оно совпадает с первичным ключом в базе данных.

```xml
<!-- WML -->
<Controls.grid:View
	source="{{_source}}"
	keyProperty="key" />
```

```js
// TypeScript
import {Memory} from "Types/source";

_source: null,
_beforeMount: function() {
    this._source = new Memory({
        keyProperty: 'key',
        data: [
            {
                key: '1',
                title: 'Ярославль'
            },
            {
                key: '2',
                title: 'Рыбинск'
            },
            {
                key: '3',
                title: 'St-Petersburg'
            }
        ]
    })
}
```

Каждый раз после загрузки данных из источника вызывается функция [dataLoadCallback](https://wasaby.dev/docs/js/Controls/grid/View/options/dataLoadCallback/). С её помощью можно дополнительно обработать данные прежде, чем отправить их в таблицу. 

Когда данные окончательного готовы, вызывается функция [itemsReadyCallback](https://wasaby.dev/docs/js/Controls/grid/View/options/itemsReadyCallback/).

Для обработки ошибок загрузки данных используйте функцию [dataLoadErrback](https://wasaby.dev/docs/js/Controls/grid/View/options/dataLoadErrback/). 

## Таблица без данных

Что делать, если в источнике данных не оказалось ни одной записи? Нужно определить, как будет выглядеть "пустая" таблица.

Это можно сделать с помощью пользовательского шаблона отображения контрола без элементов - [emptyTemplate](https://wasaby.dev/docs/js/Controls/grid/View/options/emptyTemplate/).

В примере ниже у пустой таблицы не будет колонок, а появится только указанный в параметре **contentTemplate** текст.

```xml
<!-- WML -->
<Controls.grid:View source="{{_sourse}}">
   <ws:emptyTemplate>
      <ws:partial template="Controls/grid:EmptyTemplate">
         <ws:contentTemplate>No data available!</ws:contentTemplate>
      </ws:partial>
   </ws:emptyTemplate>
</Controls.grid:View>
```

Если пользователь может сам добавлять в таблицу данные, но изначально она пустая - можно настроить вид колонок пустой таблицы с помощью [emptyTemplateColumns](https://wasaby.dev/docs/js/Controls/grid/View/options/emptyTemplateColumns/). 
```xml
<ws:emptyTemplateColumns>
	<ws:Array>
		<ws:Object startColumn="{{ 1 }}" endColumn="{{ 2 }}">
			<ws:template>
				<ws:partial template="Controls/grid:EmptyColumnTemplate">
					<ws:partial template="Controls/list:EditingTemplate" enabled="{{ true }}">
						<ws:viewTemplate>
							<div on:click="_beginAdd()">Введите наименование, штрих-код или артикул</div>
						</ws:viewTemplate>
					</ws:partial>
				</ws:partial>
			</ws:template>
		</ws:Object>
		<ws:Object startColumn="{{ 2 }}" endColumn="{{ 6 }}">
			<ws:template>
				<ws:partial template="Controls/grid:EmptyColumnTemplate" >
					<div>или выберите из <a href="#" class="controls-text-link">каталога</a></div>
				</ws:partial>
			</ws:template>
		</ws:Object>
	</ws:Array>
</ws:emptyTemplateColumns>
```

С помощью объекта [emptyTemplateOptions](https://wasaby.dev/docs/js/Controls/grid/View/options/emptyTemplateOptions/) можно задать дополнительные параметры для настройки пустой таблицы.

В примере ниже с помощью параметра **height** изменим высоту таблицы без данных.
```xml
<!-- WML -->
<Controls/grid:View source="{{_source}}">
    <ws:emptyTemplateOptions height="auto"/>
```

## Колонки

Для корректного отображения загруженных данных необходимо настроить колонки таблицы с помощью параметра [columns](https://wasaby.dev/docs/js/Controls/grid/View/options/columns/). 
```js
// TypeScript
_columns: null,
_beforeMount: function() {
   this._columns = [
      {
         displayProperty: 'name',
         align: 'left'
      },
      {
         displayProperty: 'balance',
         align: 'right'
      }
   ];
}
```
```xml
<!-- WML -->
<Controls.grid:View columns="{{_columns}}" />
```

Если колонок достаточно много, и они не помещаются на экран — можно включить горизонтальную прокрутку. За это отвечает переменная [columnScroll](https://wasaby.dev/docs/js/Controls/grid/View/options/columnScroll/).
```xml
<!-- WML -->
<Controls.grid:View
      source="{{_viewSource}}"
      columns="{{_columns}}"
      columnScroll="{{true}}"
```

Дополнительно вы можете установить начальную позицию для прокрутки ([ColumnScrollStartPosition](https://wasaby.dev/docs/js/Controls/grid/View/options/columnScrollStartPosition/)) - с начала (start, по умолчанию) или с конца столбцов (end). 
```xml
<!-- WML -->
<Controls.grid:View
   source="{{_viewSource}}"
   columns="{{_columns}}"
   columnScroll="{{true}}"
   columnScrollStartPosition="end"/>
```

Стандартным элементов прокрутки колонок является ползунок. Но его можно заменить на стрелки навигации с помощью параметра [columnScrollViewMode](https://wasaby.dev/docs/js/Controls/grid/View/options/columnScrollViewMode/).
```xml
<!-- WML -->
<Controls.grid:View
   source="{{_viewSource}}"
   columns="{{_columns}}"
   columnScroll="{{true}}"
   columnScrollViewMode="arrows"/>
```

Ещё один полезный инструмент для просмотра большого количества колонок - пролистывание с помощью захвата левой кнопкой мыши ([dragScrolling](https://wasaby.dev/docs/js/Controls/grid/View/options/dragScrolling/)). По умолчанию оно включена (true), если нет Drag'n'Drop записей.
``` xml
<!-- WML -->
<Controls.grid:View
    source="{{_viewSource}}"
    columns="{{_columns}}"
    columnScroll="{{true}}"
    dragScrolling="{{false}}" />
```

## Шапка таблицы

Шапка таблицы содержит названия колонок. По умолчанию она выключена.

Чтобы её отобразить, в конфигурации таблицы укажите значение опции [header](https://wasaby.dev/docs/js/Controls/grid/View/options/header/). В неё нужно передать массив объектов - по одному объекту на каждую колонку.
```xml
<!-- WML -->
<Controls.grid:View
	source="{{_viewSource}}"
	columns="{{_columns}}"
	header="{{_header}}" />
```
```js
// TypeScript
import { IHeaderCell } from 'Controls/grid';
...
_header: IHeaderCell[] = null;
_beforeMount(): void {
    this._header = [
        { caption: 'Страна' },
        { caption: 'Столица' },
        { caption: 'Население' }
    ]
}
```
Посмотреть, как настроить ячейки шапки, можно [здесь](https://wasaby.dev/docs/js/Controls/grid/IHeaderCell/).

Если в таблице нет данных, то по умолчанию шапка не показывается. Чтобы включить показ, установите значение параметра [headerVisibility](https://wasaby.dev/docs/js/Controls/grid/View/options/headerVisibility/) как **visible**.
```xml
<!-- WML -->
<Controls.grid:View
	source="{{_viewSource}}"
	columns="{{_columns}}"
	header="{{_header}}" 
	headerVisibility="visible" />
```

Чтобы зафиксировать шапку таблицы при прокрутке, используйте параметр [stickyHeader](https://wasaby.dev/docs/js/Controls/grid/View/options/stickyHeader/) со значением **true**.
```xml
<!-- WML -->
<Controls.grid:View
	source="{{_viewSource}}"
	header="{{_header}}"
	stickyHeader="{{true}}"/>
```


## Подвал таблицы

Помимо шапки можно настроить вид подвала таблицы. К примеру, там можно добавить определенный текст или разместить кнопку для добавления новых строк.

За конфигурацию кнопок подвала отвечает опция [footer](https://wasaby.dev/docs/js/Controls/grid/View/options/footer/).

Подвал можно настроить с помощью пользовательского шаблона ([footerTemplate](https://wasaby.dev/docs/js/Controls/grid/View/options/footerTemplate/)) (первый пример) или указать нужные колонки вручную (второй пример).
```xml
<!-- WML -->
<Controls.grid:View
	source="{{_viewSource}}"
	columns="{{_columns}}"
	<ws:footerTemplate>
		<Controls.grid:AddButton caption="Добавить новую запись"/>
	</ws:footerTemplate>
```
 
```xml
<!-- WML -->
<Controls.grid:View
   source="{{_viewSource}}" 
   columns="{{_сolumns}}">
   <ws:footer>
       <ws:Array>
           <ws:Object startColumn="{{ 2 }}" endColumn="{{ 4 }}">
               <ws:template>
                   <div>Footer column 2 - 4</div>
               </ws:template>
           </ws:Object>
           <ws:Object startColumn="{{ 5 }}" endColumn="{{ 6 }}">
               <ws:template>
                   <div>Footer column 4 - 6</div>
               </ws:template>
           </ws:Object>
       </ws:Array>
   </ws:footer>
</Controls.grid:View>
```

Чтобы зафиксировать подвал таблицы при прокрутке, используйте параметр [stickyFooter](https://wasaby.dev/docs/js/Controls/grid/View/options/stickyFooter/) со значением **true**.
```xml
<!-- WML -->
<Controls.grid:View
	source="{{_viewSource}}"
	stickyFooter="{{true}}"
	<ws:footerTemplate>
		<Controls.grid:AddButton caption="Добавить новую запись"/>
	</ws:footerTemplate>
```

## Строка итогов

Нередко в конце таблицы нужно получить сумму значений определенных колонок. Для этого включим отображение итогов с помощью опции [resultsPosition](https://wasaby.dev/docs/js/Controls/grid/View/options/resultsPosition/). 

Строчку итогов можно разместить как снизу (**bottom**), так и наверху таблицы (**top**).
```xml
<!-- WML -->
<Controls.grid:View
   source="{{_viewSource}}"
   columns="{{_columns}}"
   resultsPosition="bottom"/>
```

Если нужно вывести иную итоговую информацию - воспользуйтесь шаблоном [resultsTemplate](https://wasaby.dev/docs/js/Controls/grid/View/options/resultsTemplate/). 
```xml
<!-- WML -->
<Controls.grid:View source="{{_viewSource}}" columns="{{_columns}}" resultsPosition="bottom">
   <ws:resultsTemplate>
      <ws:partial template="Controls/grid:ResultsTemplate" scope="{{_options}}">
         <ws:contentTemplate>
            <div>Итого: 2 страны с населением более миллиарда человек</div>
         </ws:contentTemplate>
      </ws:partial>
   </ws:resultsTemplate>
</Controls.grid:View>
```

Нужна итоговая строка, даже если в таблице нет данных? Установите значение параметра [resultsVisibility](https://wasaby.dev/docs/js/Controls/grid/View/options/resultsVisibility/) как **visible**.
```xml
<!-- WML -->
<Controls.grid:View
   source="{{_viewSource}}"
   columns="{{_columns}}"
   resultsPosition="bottom"
   resultsVisibility="visible" />
```

Чтобы зафиксировать строку итогов при вертикальной прокрутке, используйте параметр [stickyResults](https://wasaby.dev/docs/js/Controls/grid/View/options/stickyResults/) со значением **true**.
```xml
<!-- WML -->
<Controls.grid:View
   source="{{_viewSource}}"
   columns="{{_columns}}"
   resultsPosition="bottom"
   stickyResults="true"/>
```

## Сортировка данных

Если в таблице много данных, то пользователю часто требуется сортировка колонок по возрастанию (**ASC**) или убыванию (**DESC**).

Сотрировка настраивается с помощью опции [sorting](https://wasaby.dev/docs/js/Controls/interface/ISorting/options/sorting/).

В примере ниже добавлена сотрировка по колонкам price и balance - для этого используется параметр [sortingProperty](https://wasaby.dev/docs/js/Controls/grid/IHeaderCell/options/sortingProperty).
```xml
<!-- WML -->
<Controls.grid:View
   source="{{_viewSource}}"
   header="{{_header}}"
   columns="{{_columns}}"
   bind:sorting="_sorting"/>
```

```js 
// TypeScript
protected _sorting: unknown = [];
protected _header: THeader = null;
protected _beforeMount(): void {
	this._sorting = [
		{price: 'DESC'},
		{balance: 'ASC'}
	],
    this._header = [
		{title: 'Цена', sortingProperty: 'price'},
		{title: 'Остаток', sortingProperty: 'balance'}
	]
}
```

## Фильтрация данных

Загруженные из источника данные можно дополнительно отфильтровать. Параметры фильтрацию задаются опцией [filter](https://wasaby.dev/docs/js/Controls/list/View/options/filter/).

В примере ниже пользователь увидит только записи с ID 1 и 2.

```xml
<!-- WML -->
<Controls.list:View
    keyProperty="id"
    filter="{{_filter}}"
    source="{{_source}}" />
```
```js
// JavaScript
this._filter = {id: ['1', '2']};
this._source = new Memory({
   keyProperty: 'id',
   data: [
      {
         id: '1',
         title: 'Yaroslavl'
      },
      {
         id: '2',
         title: 'Moscow'
      },
      {
         id: '3',
         title: 'St-Petersburg'
      }
   ]
});
```

## Визуальное оформление таблицы (стили)

### Темы

У Wasaby есть несколько предопределенных [тем оформления](https://wasaby.dev/doc/platform/themes/). Воспользуйтесь любой из них, чтобы быстро стилизовать таблицу.

```xml
<Controls.grid:View
	theme="black"/>
```

Если нужно отключить действие темы, которая применяется с контролу-родителю, то это можно сделать с помощью [notLoadThemes](https://wasaby.dev/docs/js/Controls/grid/View/options/notLoadThemes/).

Для подсветки строки при наведении на неё курсора воспользуйтесь [hoverBackgroundStyle](https://wasaby.dev/docs/js/Controls/grid/View/options/hoverBackgroundStyle/).

```xml
<!-- WML -->
<Controls.grid:View 
    source="{{_viewSource}}"
    hoverBackgroundStyle="primary" />
```

### Разделители строк и колонок

Вы можете настроить, как будут выглядеть разделительные линии между колонками и столбцами в таблице. Можно настроить их толщину или убрать их совсем.

Толщина разделителя строк определяется параметром [rowSeparatorSize](https://wasaby.dev/docs/js/Controls/grid/View/options/rowSeparatorSize). Он может принимать значения **s**, **l** или **null**.
```xml
<!-- WML -->
<Controls.grid:View
   source="{{_viewSource}}"
   columns="{{_columns}}"
   rowSeparatorSize="s"/>
```

А для колонок используйте опцию [columnSeparatorSize](https://wasaby.dev/doc/platform/controls/list/grid/columns/separator).
```xml
<!-- WML -->
<Controls.grid:View
   source="{{_viewSource}}"
   columns="{{_columns}}"
   columnSeparatorSize="s"/>
```

### Отступы внутри ячеек

С помощью опции [itemPadding](https://wasaby.dev/doc/platform/controls/list/list/paddings) можно настроить отступы внутри элементов таблицы. 

Горизонтальные отступы задаются параметрами **left** и **right**, вертикальные - **top** и **bottom**.
```xml
<!-- WML -->
<Controls.grid:View source="{{_viewSource}}">
   <ws:itemPadding top="s" bottom="s"/>
</Controls.list:View>
```

## Группировка данных

## Постраничная навигация

## Редактирование по месту

## Опции записи и контекстное меню

## Маркер

## Чекбоксы

## Перетаскивание (drag'n'drop)
