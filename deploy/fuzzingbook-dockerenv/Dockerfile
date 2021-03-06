FROM jupyter/scipy-notebook

LABEL maintainer="Nataniel Borges Jr."
LABEL description="v0.1"

USER root

# Install required APT packages
RUN apt-get update
RUN apt-get install -y --no-install-recommends graphviz inkscape bc ed texinfo groff wget
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/*

USER $NB_UID

# Install required python packages
RUN pip install matplotlib numpy pandas graphviz svglib jupyter_contrib_nbextensions autopep8 mypy notedown nbdime nbstripout

# Clone the repo
RUN cd /home/$NB_USER
RUN git clone https://github.com/uds-se/fuzzingbook.git
RUN fix-permissions /home/$NB_USER/fuzzingbook

USER root

# Activate nbdime and nbstripout
#RUN nbdime config-git --enable
RUN nbdime extensions --enable
RUN nbdime config-git --enable --global
#RUN nbstripout --install --attributes .gitattributes
RUN cd fuzzingbook

# enable nbdime for JupyterLab
RUN conda install -c conda-forge nodejs # you
#RUN jupyter labextension install nbdime-jupyterlab

# Install aditional extensions (ToC and exercise)
RUN jupyter contrib nbextension install --user
RUN jupyter nbextension enable toc2/main --user
RUN jupyter nbextension enable exercise2/main --user

#RUN jupyter extensions enable nbdime --user -py
# Other useful extensions
RUN jupyter nbextension enable codefolding/main --user
RUN jupyter nbextension enable execute_time/main --user
RUN jupyter nbextension enable varInspector/main --user
RUN jupyter nbextension enable collapsible_headings/main --user

# run matplotlib once to generate the font cache
RUN python -c "import matplotlib as mpl; mpl.use('Agg'); import pylab as plt; fig, ax = plt.subplots(); fig.savefig('test.png')"
RUN test -e test.png
RUN rm test.png

# Trust notebooks such that users can see their HTML and JS output
RUN jupyter trust /home/$NB_USER/fuzzingbook/notebooks/*.ipynb /home/$NB_USER/fuzzingbook/docs/notebooks/*.ipynb /home/$NB_USER/fuzzingbook/docs/beta/notebooks/*.ipynb

# Copy Andreas' css to jupyter
RUN mkdir /home/$NB_USER/.jupyter/custom
RUN cd /home/$NB_USER/.jupyter/custom
RUN fix-permissions /home/$NB_USER/.jupyter
RUN wget https://github.com/uds-se/fuzzingbook/blob/master/docs/beta/notebooks/custom.css
RUN ls
RUN cd /home/$NB_USER/fuzzingbook
RUN mv custom.css /home/$NB_USER/.jupyter/custom/

# Fix directory permissions
RUN fix-permissions /home/$NB_USER/.local
RUN fix-permissions /home/$NB_USER/.local/share/jupyter

# Remove temporary content
RUN apt-get clean
RUN apt-get autoremove
RUN rm -rf /var/lib/apt/lists/*

COPY start-singleuser-custom.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/start-singleuser-custom.sh
RUN mv /usr/local/bin/start-singleuser.sh /usr/local/bin/start-singleuser-old.sh
#RUN cp /usr/local/bin/custom-start.sh /usr/local/bin/start-singleuser.sh

# Quit root mode
USER $NB_UID
