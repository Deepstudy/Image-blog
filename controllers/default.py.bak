# -*- coding: utf-8 -*-
# this file is released under public domain and you can use without limitations

#########################################################################
## This is a samples controller
## - index is the default action of any application
## - user is required for authentication and authorization
## - download is for downloading files uploaded in the db (does streaming)
## - call exposes all registered services (none by default)
#########################################################################
@auth.requires_membership('manager')
def manage():
    grid = SQLFORM.smartgrid(db.image)
    return dict(grid=grid)
@auth.requires_membership('manager')
def account():
    grid = SQLFORM.smartgrid(db.auth_membership)
    return dict(grid=grid)
def user():
    return dict(form=auth())

def index():
    images = db().select(db.image.ALL, orderby=db.image.title)
    return dict(images=images)
@auth.requires_login()
def show():
    image = db.image(request.args(0)) or redirect(URL('index'))
    db.comment.image_id.default = image.id
    db.comment.author.default = auth.user.first_name +" "+auth.user.last_name
    db.comment.email.default =auth.user.email
    form=SQLFORM(db.comment,fields=['body'])
    if form.process().accepted:
        response.flash = 'your comment is posted'  
    comments = db(db.comment.image_id==image.id).select()
    imge = db(db.like.img_id==image.id).select()
    numb=0
    for i in imge:
        numb=numb+1
    return dict(image=image, comments=comments, form=form, numb=numb)
def likes():
    image = db.image(request.args(0)) or redirect(URL('index'))
    db.like.insert(img_id=image.id,usrname=auth.user.first_name+" "+auth.user.last_name)
    redirect(URL('show'+'/'+str(image.id)))
   
def download():
    return response.download(request, db)
@auth.requires_login()
def upload():
   db.image.author.default = auth.user.first_name +" "+auth.user.last_name
   form=crud.create(db.image,message='new image inserted')
   return dict(form=form)
def delete():
    image=db.image(request.args(0)) or redirect(URL('index'))
    db(db.comment.image_id==image.id).delete()
    crud.delete(db.image, image.id)
    response.flash='image deleted'
    redirect(URL('index'))
