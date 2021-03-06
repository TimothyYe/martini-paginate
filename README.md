martini-paginate
================

A pagination plugin for go-martini web framework.

## Installation

Import it into source code:

```go
import (
	"github.com/timothyye/martini-paginate"
)
```

Then get package and source code:

```bash
go get
```

## Usage

* Set paginator's handler as the middleware:

```go
m = martini.Classic()
m.Get("/casts", paginate.Handler, HandleCasts)
```

* In handler function, initialize the paginator with page size and record total amount:

```go
func HandleCasts(r render.Render, db *mgo.Database, pager *paginate.Paginator) {
	num, _ := db.C("casts").Count()

	pers := 6
	pager.Init(pers, num)

	casts := []models.Casts{}
	
	db.C("casts").Find(nil).Limit(pers).Skip(pager.Offset()).All(&casts)

	r.HTML(200, "casts", map[string]interface{}{
		"IsCasts":   true,
		"Casts":     casts,
		"Paginator": pager,
		"Num":       num})
}
```

* In template, visit the paginator and render the page:

```html
<div class="m-b-xl">
	{{if gt .Paginator.PageNums 1}}
	<ul class="pagination pagination-sm">
		{{if .Paginator.HasPrev}}
			<li><a href="{{.Paginator.PageLinkFirst}}">First</a></li>
			<li><a href="{{.Paginator.PageLinkPrev}}">&lt;</a></li>
		{{else}}
			<li class="disabled"><a>First</a></li>
			<li class="disabled"><a>&lt;</a></li>
		{{end}}
		
		{{range $index, $page := .Paginator.Pages}}
			<li{{if $.Paginator.IsActive .}} class="active"{{end}}>
				<a href="{{$.Paginator.PageLink $page}}">{{$page}}</a>
			</li>
		{{end}}
		{{if .Paginator.HasNext}}
			<li><a href="{{.Paginator.PageLinkNext}}">&gt;</a></li>
			<li><a href="{{.Paginator.PageLinkLast}}">Last</a></li>
		{{else}}
			<li class="disabled"><a>&gt;</a></li>
			<li class="disabled"><a>Last</a></li>
		{{end}}
	</ul>
	{{end}}
</div>
```

## More examples

For more examples, please refer to the source code of [Vim-Tips.com](https://github.com/TimothyYe/vim-tips-web)
