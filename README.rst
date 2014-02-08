======
torgen
======

Django's CBVs adapted to be used with Tornado along with SQLAlchemy and WTForms.
It's highly recomended to read the docs here first: https://github.com/BeardyBear/tornado-generic-handlers

0.2: new handler - DeleteHandler

Configuration
=============

The only requirement is SQLAlchemy's session stored in application's db attribute.

::

   from sqlalchemy.orm import scoped_session, sessionmaker

   class Application(tornado.web.Application):
       def __init__(self):
           self.db = scoped_session(sessionmaker(bind=engine))

Basic usage
===========

::

   from torgen.base import TemplateHandler
   from torgen.list import ListHandler
   from torgen.detail import DetailHandler
   from torgen.edit import FormHandler, DeleteHandler
   from my_alchemy_models import Post
   from my_wtforms import LoginForm

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

   class DeletePostHandler(DeleteHandler):
       template_name = 'confirm_delete.html'
       model = Post
       success_url = '/blog/'

Pagination
==========

Pagination can be used separately from generic handlers.

::

   from torgen.pagination import Paginator, EmptyPage, PageNotAnInteger

   class BlogHandler(tornado.web.RequestHandler):
       @property
       def db(self):
           return self.application.db

       def get(self, page):
           post_list = self.db.query(Post).all()
           paginator = Paginator(posts, 15)
           try:
               posts = paginator.page(page)
           except PageNotAnInteger:
               posts = paginator.page(1)
           except EmptyPage:
               posts = paginator.page(paginator.num_pages)
           self.render('blog.html', posts=posts)

You can find advanced docs here: https://github.com/BeardyBear/tornado-generic-handlers

Installation
============

Using pip:

   $ pip install torgen
