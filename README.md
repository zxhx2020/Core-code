# Core-code
import libpysal 
import spreg
import numpy as np
import geopandas
data = geopandas.read_file(r'E:\毕业论文和答辩\修改用\mgwr\mgwr.shp')
import matplotlib.pyplot as plt
import seaborn as sns
data['容积率'] = (data.area*3+data['lkmj'])/data.area
data = data.drop('OBJECTID',axis=1)
data['消费水平'] = (data['CY']+data['GW'])/data.area
data['高新企业'] = data['GSQY']/data.area
data['Area'] = data.area/1000000
data['pjczl'] = data['czl']/data.area


mgwr_final = data[['Name','pjczl','容积率','交通','消费水平','高新企业','CEI','geometry','X','Y']]
g_y = data['pjczl'].values.reshape((-1,1))
g_X = data[['容积率','交通','消费水平','高新企业','CEI']].values
u = data['X']
v = data['Y']
g_coords = list(zip(u,v))
g_X = np.log(g_X)
g_y = np.log(g_y)

from mgwr.gwr import GWR, MGWR
from mgwr.sel_bw import Sel_BW
from mgwr.utils import shift_colormap,truncate_colormap
#MGWR 
mgwr_selector = Sel_BW(g_coords, g_y, g_X, multi=True)
mgwr_bw = mgwr_selector.search(multi_bw_min=[20])
print(mgwr_bw)
mgwr_results = MGWR(g_coords, g_y, g_X, mgwr_selector).fit()

import geopandas
mgwr_final = geopandas.read_file(r'E:\毕业论文和答辩\修改用\mgwr\mgwr.shp')
#%%平均容积率
fig,ax = plt.subplots(figsize=(7,7))
from mpl_toolkits.axes_grid1 import make_axes_locatable
divider = make_axes_locatable(ax)
cax = divider.append_axes("right", size="5%", pad=0.1)
mgwr_final.plot(column='rjl',cmap=sns.cubehelix_palette(as_cmap=True),
                ax=ax,legend=True,edgecolor='k',cax=cax,legend_kwds={'label':'Average Floor Area Ratio(AFAR)'})
geopandas.GeoSeries(mgwr_final.unary_union).boundary.plot(ax=ax,color='k')
from matplotlib_scalebar.scalebar import ScaleBar
bar = ScaleBar(1,location=3)
ax.add_artist(bar)
from matplotlib import patches
a = 296000;b = 304000;c = 3466544
left = [a, c]
right = [b, c]
top = [(a+b)/2, c+14000]
center = [(a+b)/2, c+5000]
triangle = patches.Polygon([left, top, right, center], color='k')
ax.add_artist(triangle)
ax.text((a+b)/2,c+16000,'N',fontsize=15,ha='center',va='center')
ax.get_xaxis().set_visible(False)
ax.get_yaxis().set_visible(False)
#plt.savefig(r'C:\Users\13759\Desktop\给石老师论文（2）\Fig2.jpg',dpi=300)


fig,ax = plt.subplots(figsize=(7,7))
from mpl_toolkits.axes_grid1 import make_axes_locatable
divider = make_axes_locatable(ax)
cax = divider.append_axes("right", size="5%", pad=0.1)
mgwr_final.plot(column='交通',cmap="Blues",
                ax=ax,legend=True,edgecolor='k',cax=cax,legend_kwds={'label':'Public Traffic Convenience(PTC)'})
geopandas.GeoSeries(mgwr_final.unary_union).boundary.plot(ax=ax,color='k')
from matplotlib_scalebar.scalebar import ScaleBar
bar = ScaleBar(1,location=3)
ax.add_artist(bar)
from matplotlib import patches
a = 296000;b = 304000;c = 3466544
left = [a, c]
right = [b, c]
top = [(a+b)/2, c+14000]
center = [(a+b)/2, c+5000]
triangle = patches.Polygon([left, top, right, center], color='k')
ax.add_artist(triangle)
ax.text((a+b)/2,c+16000,'N',fontsize=15,ha='center',va='center')
ax.get_xaxis().set_visible(False)
ax.get_yaxis().set_visible(False)
plt.savefig(r'C:\Users\13759\Desktop\给石老师论文（2）\Fig3.jpg',dpi=300)


mgwr_final = geopandas.read_file(r'E:\毕业设计\数据\中观承载力研究区域\mgwr_final2.shp')
mgwr_final = mgwr_final.drop('OBJECTID',axis=1)
mgwr_final['Area'] = mgwr_final.area/1000000
mgwr_final['消费水平'] = (mgwr_final['CY']+mgwr_final['GW'])/mgwr_final.Area
fig,ax = plt.subplots(figsize=(7,7))
mgwr_final.plot(column='消费水平',cmap='viridis',ax=ax,
                legend_kwds={"labels":['Lower', 'Low', 'Medium', 'High', 'Higher'],'title':"CLR",'fontsize':10.5},
                legend=True,edgecolor='white',scheme='quantiles')
from matplotlib_scalebar.scalebar import ScaleBar
bar = ScaleBar(1,location=3)
ax.add_artist(bar)
from matplotlib import patches
a = 296000;b = 304000;c = 3466544
left = [a, c]
right = [b, c]
top = [(a+b)/2, c+14000]
center = [(a+b)/2, c+5000]
triangle = patches.Polygon([left, top, right, center], color='k')
ax.add_artist(triangle)
ax.text((a+b)/2,c+16000,'N',fontsize=18,ha='center',va='center')
legend = ax.get_legend()
legend.get_title().set_fontsize(11)
ax.get_xaxis().set_visible(False)
ax.get_yaxis().set_visible(False)
plt.savefig(r'C:\Users\13759\Desktop\给石老师论文（2）\Fig4.jpg',dpi=300)


mgwr_final['Area'] = mgwr_final.area/1000000
mgwr_final['高新'] = mgwr_final['GSQY']/mgwr_final.Area
fig,ax = plt.subplots(figsize=(7,7))
geopandas.GeoSeries(mgwr_final.unary_union).boundary.plot(ax=ax,color='k')
mgwr_final.plot(column='高新',cmap='Accent',ax=ax,edgecolor='k',
                legend=True,scheme='quantiles',legend_kwds={"title":'HED',"fmt": "{:.0f}",'fontsize':10})
from matplotlib_scalebar.scalebar import ScaleBar
bar = ScaleBar(1,location=3)
ax.add_artist(bar)
from matplotlib import patches
a = 296000;b = 304000;c = 3466544
left = [a, c]
right = [b, c]
top = [(a+b)/2, c+14000]
center = [(a+b)/2, c+5000]
triangle = patches.Polygon([left, top, right, center], color='k')
ax.add_artist(triangle)
ax.text((a+b)/2,c+16000,'N',fontsize=18,ha='center',va='center')
legend = ax.get_legend()
legend.get_title().set_fontsize(11)
ax.get_xaxis().set_visible(False)
ax.get_yaxis().set_visible(False)
plt.savefig(r'C:\Users\13759\Desktop\给石老师论文（2）\Fig5.jpg',dpi=300)


import geopandas
cei = geopandas.read_file(r'E:\毕业论文和答辩\修改用\城市生态指数计算\CEI_final.shp')
#%%

from matplotlib_scalebar.scalebar import ScaleBar
bar = ScaleBar(1,location=3)
from mpl_toolkits.axes_grid1 import make_axes_locatable
fig, ax = plt.subplots(1, 1,figsize=(7,7))
divider = make_axes_locatable(ax)
cax = divider.append_axes("right", size="5%", pad=0.1)
cei.plot(column='CEI',ax=ax,legend=True,edgecolor='k', cax=cax,cmap='Greens',legend_kwds={'label': "City Ecological Index(CEI)"})
ax.add_artist(bar)
boundary = geopandas.read_file(r'E:\毕业设计\数据\中观承载力研究区域\boundary.shp')
boundary.plot(ax=ax,color='k')
ax.get_xaxis().set_visible(False)
ax.get_yaxis().set_visible(False)
from matplotlib import patches
a = 296000;b = 304000;c = 3466544
left = [a, c]
right = [b, c]
top = [(a+b)/2, c+14000]
center = [(a+b)/2, c+5000]
triangle = patches.Polygon([left, top, right, center], color='k')
ax.add_artist(triangle)
ax.text((a+b)/2,c+16000,'N',fontsize=18,ha='center',va='center')
plt.savefig(r'C:\Users\13759\Desktop\给石老师论文（2）\Fig6.jpg',dpi=300)


