import imaplib
from gluon.serializers import json
import time
import uuid
#import email
from email import email
from email import message_from_string
from email.parser import HeaderParser
import os

# -*- coding: utf-8 -*-
# this file is released under public domain and you can use without limitations

#########################################################################
## This is a sample controller
## - index is the default action of any application
## - user is required for authentication and authorization
## - download is for downloading files uploaded in the db (does streaming)
## - call exposes all registered services (none by default)
#########################################################################

ImapDict = {'gmail.com':'imap.gmail.com','yahoo.com':'imap.mail.yahoo.com','students.iiit.ac.in':'students.iiit.ac.in','outlook.com':'imap-mail.outlook.com', 'research.iiit.ac.in': 'research.iiit.ac.in'}


class MailBox(object):
    def __init__(self, user, password):
        self.user = user
        self.password = password
        part = user.split('@')
        if part[1] == 'students.iiit.ac.in' or part[1] == 'research.iiit.ac.in':
            self.imap = imaplib.IMAP4(ImapDict[part[1]], 143)
            self.user=part[0]
        else:
            self.imap = imaplib.IMAP4_SSL(ImapDict[part[1]], 993)

   


 
    def __enter__(self):
        try:
        	self.imap.login(self.user, self.password)
        	return self
        except:
        	print "Invalid Credentials!!"

    def __exit__(self, type, value, traceback):
        self.imap.close()
        self.imap.logout()

    def get_count(self):
        self.imap.select('Inbox')
        status, data = self.imap.search(None, 'UNSEEN')
        return sum(1 for num in data[0].split())

    def fetch_message(self, num):
        self.imap.select('Inbox')
        status, data = self.imap.fetch(str(num), '(RFC822)')
        email_msg = email.message_from_string(data[0][1])
        return email_msg
        
    def get_msg_ids(self):
        self.imap.select('Inbox')
        ctr = 0
        msg_ids = []
        status, data = self.imap.search(None, 'UNSEEN')
        for num in reversed(data[0].split()):
        	ctr = ctr + 1
        	msg_ids = msg_ids + [num]
        	if ctr == 11:
        		break
        return msg_ids
         
    def get_msg(self,unread_msg_id):
        #print unread_msg_id
        detach_dir = '.'
        self.imap.select('Inbox')
        status1, data1 = self.imap.search(None, 'UNSEEN')
        status, data = self.imap.fetch(unread_msg_id, '(BODY.PEEK[])')
        #BODY.PEEK[] structure is used such that no changes are made to unseen flag of email structbody
        email_msg = message_from_string(data[0][1])
        #print email_msg
    	#print "hello"
    	msg = {}
    	msg['Date'] = email_msg['Date']
    	msg['From'] = email_msg['From']
    	msg['Subject'] = email_msg['Subject']
    	msg['Content'] = ""
    	#print msg
    	if email_msg.is_multipart() is False:
    		msg['Content'] = email_msg.get_payload()
    		#print msg
    		if not msg.has_key('Attachments'):
    			msg['Attachments'] = "No Attachments"
    		mymsg = "Date: " + email_msg['Date'] + "<br>From: " + msg['From'] + "<br>Subject: " + msg['Subject'] + "<br>Attachments: " + msg['Attachments'] + "<br>Content: " + msg['Content']
    		#print mymsg,"\n"
    		return mymsg
    	else:
    		lst = email_msg.get_payload()
    		#print msg['Subject']
    		bodytext = lst[0].get_payload()
    		if isinstance(bodytext,str):
    			msg['Content'] = bodytext
    		#print msg
    		#else:
    		#print "HELLO: EMPTY"
    		#msg['Attachments'] = ""
    		#print msg
    	mymsg = "Date: " + msg['Date'] + "<br>From: " + msg['From'] + "<br>Subject: " + msg['Subject'] + "<br>Content: " + msg['Content']
    	for part in email_msg.walk():
    		if part.get_content_maintype() == 'multipart':
    			#print "hi"
    			continue
    		if part.get('Content-Disposition') is None:
    			#print "hi"
    			continue
    		#print "hey"
    		fileName = part.get_filename()
    		#print "hiii"
    		if bool(fileName):
    			#print "hi"
    			if msg.has_key('Attachments'):
    				msg['Attachments'] = msg['Attachments'] + fileName + "<br>"
    				#print msg['Attachments']
    			else:
    				msg['Attachments'] = fileName + "<br>"
    				#print msg['Attachments']
				if 'attachments' not in os.listdir(detach_dir):
					os.mkdir('attachments')
				filePath = os.path.join(detach_dir, 'attachments', fileName)
				if not os.path.isfile(filePath) :
					#print "File:",fileName
					fp = open(filePath, 'wb')
					fp.write(part.get_payload(decode=True))
					fp.close()
			continue
		if not msg.has_key('Attachments'):
			msg['Attachments'] = "No Attachments"
		#print msg
		mymsg = "Date:" + msg['Date'] + "<br>From:" + msg['From'] + "<br>Subject:" + msg['Subject'] + "<br>Attachments:" + msg['Attachments'] + "<br>Content: " + msg['Content']
		#mymsg = "{'Date': 'ab'}"
		#print mymsg,"\n"
		return mymsg

	def getmsg(self,unread_msg_id):
		#print unread_msg_id
		detach_dir = '.'
		self.imap.select('Inbox')
		status1, data1 = self.imap.search(None, 'UNSEEN')
		status, data = self.imap.fetch(unread_msg_id, '(BODY.PEEK[])')
		#BODY.PEEK[] structure is used such that no changes are made to unseen flag of email structbody
		email_msg = message_from_string(data[0][1])
		#print "hello"
		msg = {}
		msg['Date'] = email_msg['Date']
		msg['From'] = email_msg['From']
		msg['Subject'] = email_msg['Subject']
		#print msg
		if email_msg.is_multipart() is False:
			msg['Content'] = email_msg.get_payload()
			#print msg
			if not msg.has_key('Attachments'):
				msg['Attachments'] = "No Attachments"
			mymsg = "Date: " + email_msg['Date'] + "<br>From: " + msg['From'] + "<br>Subject: " + msg['Subject'] + "<br>Attachments: " + msg['Attachments'] + "<br>Content: " + msg['Content']
			#print mymsg,"\n"
			return mymsg
		else:
			lst = email_msg.get_payload()
    		#print msg['Subject']
    		bodytext = lst[0].get_payload()
    		if isinstance(bodytext,str):
    			msg['Content'] = bodytext
    		#print msg
    		#else:
    		#print "HELLO: EMPTY"
    		#msg['Attachments'] = ""
    		#print msg
    	#mymsg = "Date:" + msg['Date'] + ",From:" + msg['From'] + ",Subject:" + msg['Subject'] + ",Content: " + msg['Content']
    	if not msg.has_key('Attachments'):
    		msg['Attachments'] = "No Attachments"
    	#print msg
    	mymsg = "Date: " + msg['Date'] + "<br>From: " + msg['From'] + "<br>Subject: " + msg['Subject'] + "<br>Attachments: " + msg['Attachments'] + "<br>Content: " + msg['Content']
    	#mymsg = "{'Date': 'ab'}"
    	#print mymsg,"\n"
    	return mymsg

def index():
    """
    example action using the internationalization operator T and flash
    rendered by views/default/index.html or views/generic.html

    if you need a simple wiki simply replace the two lines below with:
    return auth.wiki()
    """
    response.flash = T("Welcome to web2py!")
    return dict(message=T('Hello World'))


def user():
    """
    exposes:
    http://..../[app]/default/user/login
    http://..../[app]/default/user/logout
    http://..../[app]/default/user/register
    http://..../[app]/default/user/profile
    http://..../[app]/default/user/retrieve_password
    http://..../[app]/default/user/change_password
    http://..../[app]/default/user/manage_users (requires membership in
    use @auth.requires_login()
        @auth.requires_membership('group name')
        @auth.requires_permission('read','table name',record_id)
    to decorate functions that need access control
    """
    return dict(form=auth())

@cache.action()
def download():
    """
    allows downloading of uploaded files
    http://..../[app]/default/download/[filename]
    """
    return response.download(request, db)


def call():
    """
    exposes services. for example:
    http://..../[app]/default/call/jsonrpc
    decorate with @services.jsonrpc the functions to expose
    supports xml, json, xmlrpc, jsonrpc, amfrpc, rss, csv
    """
    return service()


@auth.requires_signature()
def data():
    """
    http://..../[app]/default/data/tables
    http://..../[app]/default/data/create/[table]
    http://..../[app]/default/data/read/[table]/[id]
    http://..../[app]/default/data/update/[table]/[id]
    http://..../[app]/default/data/delete/[table]/[id]
    http://..../[app]/default/data/select/[table]
    http://..../[app]/default/data/search/[table]
    but URLs must be signed, i.e. linked with
      A('table',_href=URL('data/tables',user_signature=True))
    or with the signed load operator
      LOAD('default','data.load',args='tables',ajax=True,user_signature=True)
    """
    return dict(form=crud())

def signup():
	return dict()

def signup_2():
	"""
	Filling the Database
	"""
	Name=request.vars.Name
	Username=request.vars.Username
	Password=request.vars.Password
	C_Password=request.vars.C_Password
	EmailID1=request.vars.EmailID1
	Password1=request.vars.Password1
	Email1=(EmailID1,Password1)
	EmailList=[Email1]
	
	if(request.vars.EmailID2):
		EmailID2=request.vars.EmailID2
		Password2=request.vars.Password2
		Email2=(EmailID2,Password2)
		EmailList.append(Email2)
	if(request.vars.EmailID3):
		EmailID3=request.vars.EmailID3
		Password3=request.vars.Password3
		Email3=(EmailID3,Password3)
		EmailList.append(Email3)
	if(request.vars.EmailID4):
		EmailID4=request.vars.EmailID4
		Password4=request.vars.Password4
		Email4=(EmailID4,Password4)
		EmailList.append(Email4)
	if(request.vars.EmailID5):
		EmailID5=request.vars.EmailID5
		Password5=request.vars.Password5
		Email5=(EmailID5,Password5)
		EmailList.append(Email5)
	
	Organisation = None
	
	if(request.vars.Organisation):
		Organisation=request.vars.Organisation
		
	ProjectList = []
	
	if(request.vars.Project1):
		Project1=request.vars.Project1
		ProjectList.append(Project1)		
	if(request.vars.Project2):
		Project2=request.vars.Project2
		ProjectList.append(Project2)
	if(request.vars.Project3):
		Project3=request.vars.Project3
		ProjectList.append(Project3)
		
	"""
	Validate Emails
	"""
	for email in EmailList:
		eid,pwd=email
		print eid
		eidpart=eid.split('@')
		if len(eidpart) != 2:
			response.flash = "Invalid Email ID!"
			print "invalid email id"
			return dict()
		if eidpart[1] not in ImapDict:
			response.flash = "Invalid Email ID!"
			print "invalid emailid"
			return dict()
		if eidpart[1] == 'students.iiit.ac.in' or eidpart[1] == 'research.iiit.ac.in':
			imap = imaplib.IMAP4(ImapDict[eidpart[1]], 143)
			try:
				imap.login(eidpart[0],pwd)
			except:
				response.flash = "Invalid Email ID!"
				print "invalid emailid"
				return dict()
		else:
			imap = imaplib.IMAP4_SSL(ImapDict[eidpart[1]], 993)
			try:
				imap.login(eid,pwd)	
			except:
				response.flash = "Invalid Email ID!"
				print "invalid email id"
				return dict()
	
	"""
	Check if username already exists
	"""
	for i in db(db.Users.Username == Username).select():
		response.flash = "Username already exists!"
		print "username exists"
		return dict()
	
	
	"""
	Check if passwords match
	"""
	if Password != C_Password:
		response.flash = "Passwords don't match!"
		print "passwords dont match"
		return dict()
	
	"""
	If all details are correct, fill the database and redirect to login page
	"""
	db.Users.insert(Name=Name,Username=Username,Password=Password,Organisation=Organisation)
	db.Projects.insert(Username=Username)
	db.EmailIds.insert(Username=Username,EID1=EmailList[0][0],Pass1=EmailList[0][1])
	
	leneid = len(EmailList)
	
	if leneid > 1:
		db(db.EmailIds.Username == Username).update(EID2=EmailList[1][0],Pass2=EmailList[1][1])
	if leneid > 2:
		db(db.EmailIds.Username == Username).update(EID3=EmailList[2][0],Pass3=EmailList[2][1])
	if leneid > 3:
		db(db.EmailIds.Username == Username).update(EID4=EmailList[3][0],Pass4=EmailList[3][1])
	if leneid > 4:
		db(db.EmailIds.Username == Username).update(EID5=EmailList[4][0],Pass5=EmailList[4][1])
	
	lenproj = len(ProjectList)

	if lenproj > 0:
		db(db.Projects.Username == Username).update(Project1=ProjectList[0])
	if lenproj > 1:
		db(db.Projects.Username == Username).update(Project2=ProjectList[1])
	if lenproj > 2:
		db(db.Projects.Username == Username).update(Project3=ProjectList[2])
		
	redirect(URL('login'))

def cleardb():
	db.Users.truncate()
	db.Projects.truncate()
	db.EmailIds.truncate()
	db.memo.truncate()

def login():
	return dict()

def login_2():
	flag = 0
	Username = request.vars.Username
	Password = request.vars.Password
	for i in db(db.Users.Username == Username).select():
		flag = 1
		if i['Password'] == Password:
			session.Username = Username
			redirect(URL('home.html'))
		else:
			response.flash = "Wrong Password!"
	if flag == 0:
		response.flash = "Username doesn't exist!"
	return dict()
	
def email():
	if session.Username:
		for user in db(db.EmailIds.Username == session.Username).select():
			ctr = 1
			if user['EID2']:
				ctr = ctr + 1
			if user['EID3']:
				ctr = ctr + 1
			if user['EID4']:
				ctr = ctr + 1
			if user['EID5']:
				ctr = ctr + 1
			emailids = []
			unreads = []
			unread_messages = []
			for i in range(ctr):
				i = i + 1
				emailid = 'EID' + str(i)
				pwdid = 'Pass' + str(i)
				eid = user[emailid]
				pwd = user[pwdid]
				emailids.append(eid)
				with MailBox(eid,pwd) as mbox:
					unread = mbox.get_count()
					#x = 'unread' + str(i)
					unreads.append(unread)
					unread_msg_ids = mbox.get_msg_ids()
					unread_msgs = []
					#print unread_msg_ids
					if unread_msg_ids != []:
						ctr2 = 0
						for i in unread_msg_ids:
							ctr2 = ctr2 + 1
							if ctr2 == 11:
								break
							unread_msg = get_msg(eid,pwd,unread,i)
							unread_msgs.append(unread_msg)
					#y = 'unread_msgs' + str(i)
					unread_messages.append(unread_msgs)
			return dict(ctr=ctr,emailids=json(emailids),unreads=json(unreads),unread_messages=json(unread_messages))
		#return dict(unread1=unread1,unread_msg_ids=json(unread_msg_ids))
	else:
		redirect(URL('login'))
def get_msg(eid,pwd,unread,unread_msg_id):
	with MailBox(eid,pwd) as mbox:
		#print "hello"
		#print request.get_vars['unread_msg_id']
		#unread_msg = mbox1.get_msg(request.get_vars['unread_msg_id'])
		#print type(unread_msg),"hi"
		unread_msg = mbox.get_msg(unread_msg_id)
		if unread_msg == None:
			unread_msg = mbox.getmsg(unread_msg_id)
		#print unread_msg,"hello"
		return unread_msg
			
	
def logout():
	if session.Username:
		del session.Username
		return dict()
	return dict()

def home():
	return dict()

def gotopage():
	return dict()

def insert():
	dated = request.get_vars['dated']
	msg = request.get_vars['msg']
	msg = '<li>' + msg +'</li>'

#	rows = db(db.memo.uname == 'shikha').select()
#	for row in rows:
#		if row['dated'] == dated:
#			id = row['id']
#			msg = row['msg']+ msg
#			db(db.memo.id == row['id']).update(msg =msg)
#			break


#	else:
	db.memo.insert(uname='shikha',dated= dated,msg = msg)
	return dict()

def show():
	
	dated= request.get_vars['dated']
	rows = db(db.memo.dated == dated).select()
#	text= '{ "text" : ['
	text ='<ul>'
	for row in rows:
		if row['uname'] == 'shikha' :
			text += row['msg']

#	text += ']}'
	text += '</ul>'
	return dict(text= text)


def showall():
	dated= request.get_vars['dated']

	selectdate = str(dated).split('-')
	month = selectdate[1]
	string ='<ul>'
	rows = db(db.memo.uname == 'shikha').select()
	string = 'month : '+ month+'<ul>'
	for i in range(1,31):
		text=''
		for row in rows:
			if str(row['dated']) == str(selectdate[0])+'-'+str(month)+'-'+str(i):
				text += row['msg']
				datee =  str(selectdate[0])+'-'+str(month)+'-'+str(i)
		if text != '':
			string += '<li>Date : '+ datee + '<ul>' + text +'</ul></li>'
	string+= '</ul>'
#		var = str(row['dated']).split('-')
#		if var[1] == month:
			
#			if prev == row['dated']:
#				text += row['msg']
#			else:
#				text+='</ul></li><li>Date: ' +str(row['dated']) +'<ul>'+  row['msg']
#			prev = row['dated']
		

#	text+='</ul>'
	return dict(text=string)

	


def show_month():

	string ='<ul>'
	selectyear = request.get_vars['selectyear']
	month = request.get_vars['month']
	rows = db(db.memo.uname == 'shikha').select()
	string = 'month : '+ month+'<ul>'
	for i in range(1,31):
		text=''
		for row in rows:
			if str(row['dated']) == str(selectyear)+'-'+str(month)+'-'+str(i):
				text += row['msg']
				datee =  str(selectyear)+'-'+str(month)+'-'+str(i)
		if text != '':
			string += '<li>Date : '+ datee + '<ul>' + text +'</ul></li>'
	string+= '</ul>'
	return dict(text=string)

def googledrive():
	if session.Username:
		return dict()
	else:
		redirect(URL('login'))
def display():
	user = request.get_vars['user']
	no = request.get_vars['no']
		
	rows = db(db.my_notes.notes_user == user).select()
	text = '"'
#	if no == '1':
#		for row in rows:
#			text += row['note1'] 
#	if no =='2':
#		for row in rows:
#			text += row['note2'] 
#	if no =='3':
#		for row in rows:
#			text += row['note3'] 
#	if no =='4':
#		for row in rows:
#			text += row['note4'] 
#	if no =='5':
#		for row in rows:
#			text += row['note5'] 
#	if no =='6':
	for row in rows:
		text += row['note'+no] 
	text+= '"'
	return dict(note1 = text)

def save():
	
	user = request.get_vars['user']
	no = request.get_vars['no']
	if no =='1':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note1 = request.get_vars['text'])
	if no =='2':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note2 = request.get_vars['text'])
	if no =='3':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note3 = request.get_vars['text'])
	if no =='4':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note4 = request.get_vars['text'])
	if no =='5':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note5 = request.get_vars['text'])
	if no =='6':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note6 = request.get_vars['text'])

	#db(db.my_notes.notes_user == user).select(db.my_notes.note1)
	return dict()

def notes():
	return dict(user=session.Username)

def delt():
	user = request.get_vars['user']
	#db(db.my_notes.notes_user == user).delete()
	#db(d).delete()
	no = request.get_vars['no']
	if no == '1':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note1 ='')
	if no == '2':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note2 ='')
	if no == '3':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note3 ='')
	if no == '4':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note4 ='')
	if no == '5':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note5 ='')
	if no == '6':
		db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note6 ='')
	return dict()

def deleteall():
	user = request.get_vars['user']
	#db(db.my_notes.notes_user == user).delete()
	#db(d).delete()
	db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note1 ='')
	db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note2 ='')
	db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note3 ='')
	db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note4 ='')
	db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note5 ='')
	db.my_notes.update_or_insert(db.my_notes.notes_user == user, notes_user = user,note6 ='')

	return dict()
