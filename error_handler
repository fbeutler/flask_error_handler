'''
Within the application we can raise errors which are caught with this handler 

err_msg = ("This journal club does not exist.")
raise NotFound(description=err_msg)

This implementation mainly follows the suggestion presented here:
https://realpython.com/blog/python/python-web-applications-with-flask-part-iii/
'''
from flask import current_app, Markup, render_template, request
from flask_login import current_user
from werkzeug.exceptions import default_exceptions, HTTPException


def error_handler(error):
    ''' Catches all errors in the default_exceptions list '''
    msg = "Request resulted in {}".format(error)
    if current_user.is_authenticated:
        current_app.logger.error(msg, exc_info=error)
    else:
        current_app.logger.warning(msg, exc_info=error)

    if isinstance(error, HTTPException):
        description = error.get_description(request.environ)
        code = error.code
        name = error.name
    else:
        description = ("We encountered an error "
                       "while trying to fulfill your request")
        code = 500
        name = 'Internal Server Error'

    # Flask supports looking up multiple templates and rendering the first
    # one it finds.  This will let us create specific error pages
    # for errors where we can provide the user some additional help.
    templates_to_try = ['errors/error{}.html'.format(code), 'errors/generic_error.html']
    return render_template(templates_to_try,
                           code=code,
                           name=Markup(name),
                           description=Markup(description),
                           error=error), code


def init_app(app):
    ''' Function to register error_handler in app '''
    for exception in default_exceptions:
        app.register_error_handler(exception, error_handler)

    app.register_error_handler(Exception, error_handler)
