========================
tornado-generic-handlers
========================

Basic usage
=====

::

   from torgen.base import TemplateHandler
   from torgen.list import ListHandler
   from torgen.detail import DetailHandler
   from torgen.edit import FormHandler

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

You can find more docs here: https://github.com/BeardyBear/tornado-generic-handlers

Installation
============

You can to use pip_ to install tornado-generic-handlers::

   $ pip install torgen

Or using last source::

   $ pip install git+git://github.com/BeardyBear/tornado-generic-handlers.git
