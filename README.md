<h1>tornado-generic-handlers</h1>
<p>This package contains Django's generic class based views adapted to be used with Tornado along with SQLAlchemy and WTForms. 
Note that implementation might differ a bit in some of the cases.<br/> The features included:
<ul>
  <li>generic handlers</li>
  <li>pagination</li>
  <li>project-wide context variables</li>
</ul>
</p>
<h2>Installation</h2>
```
pip install torgen
```
<h2>Configuration</h2>
```python
#app.py
from sqlalchemy.orm import scoped_session, sessionmaker

class Application(tornado.web.Application):
    def __init__(self):
        self.db = scoped_session(sessionmaker(bind=engine))
```
<h2>Basic usage</h2>
```python
from torgen.base import TemplateHandler
from torgen.list import ListHandler
from torgen.detail import DetailHandler

class HomeHandler(TemplateHandler):
    template_name = 'home.html'
    
class BlogHandler(ListHandler):
    template_name = 'blog.html'
    paginate_by = 10
    context_object_name = 'post_list'
    model = Post
    
class PostHandler(DetailHandler):
    template_name = 'post.html'
    model = Post
    context_object_name = 'post'
    
class LoginHandler(FormHandler):
    template_name = 'login.html'
    form_class = LoginForm
    success_url = '/'
    
    def form_valid(self, form):
        self.set_secure_cookie('user', form.data['username'])
        return super(LoginHandler, self).form_valid(form)
```
<h2>More on handlers</h2>
<p>You'd like to override handlers methods to customize their behaviour. </p>
<h3>FormHandler</h3>
<p>A handler that displays a form. On error, redisplays the form with validation errors; on success, redirects to a new URL.</p>
```python
class CreatePostHandler(FormHandler):
    template_name = 'create_post.html'
    form_class = CreatePostForm
    initial = {'title': 'Default title'} #initial value for the form field
        
    def get_initial(self):
        """
        Returns the copy of provided initial dictionary.
        If you need, return the whole new dictionary from here instead of updating it.
        """
        dummy_text = self.db.query(DummyTexts).first()
        self.initial.update({'text': dummy_text})
        return super(CreatePostHandler, self).get_initial()
    
    def form_valid(self, form):
        """
        Called if the form was valid. Redirect user to success_url.
        """
        post = Post(title=form.data['title'], text=form.data['text'])
        self.db.add(post)
        self.db.commit()
        self.db.refresh(post)
        self.post_id = post.id
        return super(CreatePostHandler, self).form_valid(form)
        
    def form_invalid(self, form):
        """
        Called if the form was invalid.
        By default it rerenders template with the form holding error messages.
        Here you can add new context variables or do anythin you'd like, i.e.
        redirect user somewhere.
        """
        new_var = 'brand new variable to the template context'
        return self.render(self.get_context_data(form=form, new_var=new_var))
        
    def get_success_url(self):
        """
        Returns success_url attribute by default.
        """
        return self.reverse_url('post', self.post_id)
        
    def get_form_kwargs(self):
        """
        Returns kwargs that will be passed to your form's constructor.
        """
        kwargs = super(CreatePostHandler, self).get_form_kwargs()
        kwargs['variable'] = 'some variable to be here'
        return kwargs
```
<h3>DetailHandler</h3>
<p>While this handler is executing, self.object will contain the object that the handler is operating upon.</p>
```python
class PostDetailHandler(DetailHandler):
    """
    Displays the object with provided id.
    """
    template_name = 'post.html'
    model = Post
    #name by which the object will be accessible in template
    context_object_name = 'post' 

class Application(tornado.web.Application):
    def __init__(self):
        handlers = [
            url(r'/post/(?P<id>\d+)/', PostDetailHandler, name='post_detail'),
        ]
```
```python
class PostDetailHandler(DetailHandler):
    """
    The same, but with modified url kwarg name.
    """
    template_name = 'post.html'
    model = Post
    context_object_name = 'post' 
    pk_url_kwarg = 'super_id'

class Application(tornado.web.Application):
    def __init__(self):
        handlers = [
            url(r'/post/(?P<super_id>\d+)/', PostDetailHandler, name='post_detail'),
        ]
```
```python
class PostDetailHandler(DetailHandler):
    """
    Displays the object by its slug.
    Surely, the slug field doesn't need to be a slug really.
    """
    template_name = 'post.html'
    model = Post
    context_object_name = 'post' 
    slug_url_kwarg = 'mega_slug' #defaults to slug
    slug_field = Post.slug_field_name

class Application(tornado.web.Application):
    def __init__(self):
        handlers = [
            url(r'/post/(?P<mega_slug>[-_\w]+)/', PostDetailHandler, name='post_detail'),
        ]
```
